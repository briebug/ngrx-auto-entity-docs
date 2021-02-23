---
description: This goes with that...
---

# Correlation

Since Auto-Entity is a complete solution that manages your entities for you, including calling entity services and handling their results, your own code is a bit "out of the loop" when it comes to handling results that correlate directly to some particular initiating action. 

For example, if you create an entity, you dispatch an action. When that entity has been created, another action, a success or failure result action, will be dispatched. Since these two events are now disjoint and decoupled from each other, handling a particular result for a particular initiation requires some means of correlating the two. 

### The Correlation Id

Auto-Entity provides a built-in mechanism for handling correlation. Every action is equipped with a `correlationId` that allows result actions to be associated back to the action that initiated them. Every initiating action created by auto-entity will have a correlationId generated for it automatically, however manually providing the correlationId is also an option. 

Auto-Entity will also ensure that whenever a result action, success or failure, is created that the correlationId of those actions will match the correlationId of the initiating action that started the process. This is true whether the `correlationId` is created automatically or provided manually. 

### Patterns for Correlating Actions

There are some basic patterns for correlating Auto-Entity actions using the `correlationId`. The most basic is **The Leap**, where a custom action is used to make the little "leap" from the effect that is dispatching the initiating action, say `Create`, to the effect\(s\) that will handle the result actions, `CreateSuccess` or `CreateFailure`. This action will contain at the very least, the correlationId of the initiating action.

#### Implementing The Leap

Correlation itself simply requires checking if the `correlationId` of the leap action matches the `correlationId` of the result action. First, set up an effect to handle initiation:

{% tabs %}
{% tab title="Effect" %}
```typescript
createCustomer$ = createEffect(
  () => this.actions$.pipe(
    ofType(newCustomerCreateButtonClicked),
    map(({ customer }) => new Create(Customer, customer)),
    switchMap(action => [action, customerCreationCompleted({ correlationId: action.correlationId }) ])
  )
);
```
{% endtab %}

{% tab title="Action" %}
```typescript
export const customerCreationCompleted = createAction(
  '[Customer] (Creation) Completed',
  props<{ correlationId: string }>()
);
```
{% endtab %}
{% endtabs %}

This is really all there is to it at a basic level. There are other ways to handle this as well...you could create the `correlationId` yourself and avoid having to pre-create the action. Note that the `switchMap` part here, which could be `exhaustMap` or `concatMap` or `mergeMap` as appropriate for the use case, will automatically convert the returned array into a stream, one that emits twice, first the initiating action and second the leap action. 

Once you have set up your leap action, you can correlate the result action in another effect or effects. To handle success and failure separately, two effects can be created to handle the different paths. Perhaps customer creation is performed within a modal popup that must be closed on successful completion, whereas only an alert toast should be shown on failure. 

```typescript
closeCustomerCreationModal$ = createEffect(
  () => combineLatest([
    this.actions$.pipe(ofEntityType(Customer, EntityActionTypes.CreateSuccess)),
    this.actions$.pipe(ofType(customerCreationCompleted)),
  ]).pipe(
    filter(([{ correlationId }, { correlationId: expectedCorrelationId }]) => correlationId === expectedCorrelationId),
    tap(() => this.customerUI.closeCreateModal())
  ), 
  { dispatch: false }
);

toastCustomerCreationError$ = createEffect(
  () => combineLatest([
    this.actions$.pipe(ofEntityType(Customer, EntityActionTypes.CreateFailure)),
    this.actions$.pipe(ofType(customerCreationCompleted)),
  ]).pipe(
    filter(([{ correlationId }, { correlationId: expectedCorrelationId }]) => correlationId === expectedCorrelationId),
    tap(([{ entity, error }]) => this.customerUI.toastError({entity, error}))
  ), 
  { dispatch: false }
)
```

The basic pattern here uses a `filter` operator to ensure only the matching result for the initiation captured by the leap action is handled. If the filter passes, then whatever remaining work may be performed with the appropriate result action.

