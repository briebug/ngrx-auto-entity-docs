---
description: 'Whats mine is yours, whats yours is mine!'
---

# Integrating Custom Effects

When the need arises to create a **custom action** & **custom effects**, Auto-Entity can still help you reduce your workload. It may be that you can integrate your actions, effects and data with Auto-Entity managed state, so long as the data is compatible. 

### Custom Action

As a simple example, you may wish to create a custom action for handling type-ahead lookups that search a given entity. 

{% code title="customer.actions.ts" %}
```typescript
export const SearchCustomers = createAction(
  '[Customer] Typeahead Search',
  props<{criteria: string }>()
);
```
{% endcode %}

### Custom Effect

You could then implement your own effect for handling this action to ensure the proper implementation and behavior:

{% code title="customer.effects.ts" %}
```typescript
import {Actions, createEffect} from '@ngrx/effects';
import {LoadAllSuccess, LoadAllFailure} from '@briebug/ngrx-auto-entity';
import {SearchCustomers} from 'state/customer.actions';

export class CustomerEffects {
    searchCustomers$ = createEffect(
        () => this.actions$.pipe(
            ofType(SearchCustomers),
            map(action => action.criteria),
            switchMap(criteria => 
                this.customerService.search(criteria).pipe(
                    map(result => new LoadAllSuccess(Customer, result)),
                    catchError(err => of(new LoadAllFailure(Customer, err)))
                )
            )
        ),
        { resubscribeOnError: false }
    );
    
    constructor(private actions$: Actions, private customerService: CustomerService) {}
}
```
{% endcode %}

In this case, we need `switchMap` semantics...that is, we wish to cancel any previous requests that may have stale information in order to utilize the most up to date search criteria the user may have entered. 

{% hint style="info" %}
By default, Auto-Entity uses `mergeMap` for all effects, which may not always be the most appropriate mapper for the use case. The default use of `mergeMap` ensures that all entity actions for any entity dispatched within the store may operate concurrently, without queueing up, blocking, canceling, or dropping actions for other entities. 
{% endhint %}

### Integrate into Auto-Entity Managed State

Take note, however, of the new actions returned by this effect:

```typescript
this.customerService.search(criteria).pipe(
    map(result => new LoadAllSuccess(Customer, result)),
    catchError(err => of(new LoadAllFailure(Customer, err)))
))
```

We are returning generic actions! Since these are `customers`, as long as we have built auto-entity state for the `Customer` entity, you may combine custom actions you implement yourself with generic actions from the Auto-Entity library. 

These actions will ultimately cause the returned customer search results to be reduced into state managed by Auto-Entity for you. You only have to create just one action. Just make sure the shape of the data returned by your custom service is compatible, or is otherwise transformed into a compatible shape.

