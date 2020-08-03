---
description: Doing more with less...
---

# Composing Actions

When developing complex applications, sometimes it may help to "bundle" multiple actions together or compose many individual actions into sort of a higher order action, an action of actions. Such actions may be handled by custom effects to enforce order-of-execution in a manner that may be much more challenging otherwise.

### Use Case

A simple use case might be the creation of an `Order` that contains `LineItem`s. Depending on the nature of the backend system you must work with, it may be that you must first create an empty order which returns its key, then populate that order, for which you now know the key, with items that relate back to the order. You may have certain timing issues here that are not necessarily easy to solve with the asynchronous nature of the web and NgRx.

{% hint style="info" %}
NgRx Auto-Entity 0.5 introduced the concept of action correlation. By attaching a unique correlation id to every auto-entity initiating action, and threading that same correlation id through each result action, correlation between initiation and result can be achieved.

This allows complex scenarios that require proper coordination of multiple steps or stages to be accomplished. Even when such scenarios dictate that complex workflows be broken up across many effects.
{% endhint %}

### Full Order Creation Action

Start by creating a custom action, `createFullOrder`. This action will require both an `Order` entity as well as an array of `LineItem` entities. We only need this one action, as we will ultimately leverage existing Auto-Entity actions down the line. Lets just put this new action in the state file for our order state:

{% code title="state/order.state.ts" %}
```typescript
// ... other order state config...

export const createFullOrder = createAction(
    '[Orders] Create Full Order',
    props<{ 
        order: Readonly<Order>, 
        lineItems: ReadonlyArray<LineItem>,
        correlationId: string
    }>()
);
```
{% endcode %}

Note that this action includes a third property: correlationId. This is a unique identifier \(say a uuid\) that allows us to correlate this action with subsequent auto-entity actions dispatched across many effects.

With this action now in hand, we want to update our Order facade to support creation of a full order with its line items. We can simple add a new method to our facade that will facilitate this new behavior:

{% code title="facades/order.facade.ts" %}
```typescript
import { createFullOrder, OrderFacadeBase } from '../state/order.state';

export class OrderFacade extendes OrderFacadeBase {
    constructor(store: Store<AppState>) {
        super(Order, store);
    }
    
    createOrderWithLineItems(
        order: Readonly<Order>, 
        lineItems: ReadonlyArray<LineItem>
    ): void {
        this.store.dispatch(createFullOrder({
            order, 
            lineItems, 
            correlationId: uuid()
        });
    }
}
```
{% endcode %}

### Order Creation Custom Effect

In order to leverage NgRx Auto-Entity here, we will need to create some effects that decompose our createFullOrder action into a generic Create action for the Order, then upon the success of said order creation dispatch additional generic Create actions for each LineItem. 

This order of execution is important as creation of a LineItem entity requires knowledge of the Order they belong to, and the key for this order is only known once it is created. Achieving it can be a little tricky, but it tends to be easier in an effect than elsewhere:

{% code title="state/order.effects.ts" %}
```typescript
export class OrderEffects {
    constructor(private actions$: Actions, private store: Store<AppState>) {}
    
    initiateFullOrderCreation$ = createEffect(
        () => this.actions$.pipe(
            ofType(createFullOrder),
            map(({order}) => new Create(Order, order)))
        )
    );
    
    continueOrderLineItemCreation$ = createEffect(
        () => combineLatest([
            this.actions$.pipe(
                ofType(createFullOrder)
            ),
            this.actions$.pipe(
                ofEntityType(Order, EntityActionTypes.CreateSuccess)
            )
        ]).pipe(
            filter(([{correlationId}, {correlationId: createOrderCID}]) =>
                correlationId === createOrderCID
            ),
            map(([{lineItems, correlationId}, {entity}]) => ({
                order: entity,
                lineItems,
                correlationId
            })),
            concatMap(({order, lineItems}) =>
                lineItems.reduce((_, item) => 
                    new Create(LineItem, item, undefined, correlationId)
                )
            )
        )
    );
}
```
{% endcode %}

{% hint style="warning" %}
Don't forget register your new `OrderEffects` class with the NgRx `EffectsModule`!
{% endhint %}

This new effect will now handle dispatching of `Create` generic actions for our new `Order`, then wait for a `CreateSuccess` generic action from Auto-Entity to be dispatched for the `Order` entity type, and upon said dispatch occurring will then dispatch additional `Create` generic actions for each of the `LineItem`s for that order.

### Best in an Effect

This careful timing of dispatches may be achieved elsewhere, such as a component or a facade, however the interactions will generally be more complex and disjoint than when handled in a cohesive effect. Leverage the full power of NgRx when you need to!

