---
description: Radical Simplification with Reusable Action Libraries
---

# Reusable Generic Actions

NgRx Auto-Entity provides a relatively simple solution to the _**action triplet trifurcation conundrum**_. Make commonly-implemented actions reusable! If there ever was a use case for generics, CRUD entity actions would seem to be as sublimely perfect a case as ever possible. By making actions **generic**, this allows a simple library of standard CRUD actions to be implemented that could then be easily reused by any number of unique entities. 

### Actions Made For You

Generic actions are the primary interface with which an application interacts with NgRx Auto-Entity. No need to implement the same pattern of three actions again and again for each and every entity! We have provided all the necessary actions for you. You simply need to dispatch them! 

{% code title="customers.component.ts" %}
```typescript
import {Create, LoadAll} from '@briebug/ngrx-auto-entity';
import {Customer} from 'models';
import {allCustomers} from 'state/customer.state';

@Component(...)
export class CustomersComponent implements OnInit {
    customers$: Observable<Customer[]>;
    
    constructor(
        private activatedRoute: ActivatedRoute, 
        private store: Store<AppState>
    ) {}
    
    ngOnInit() {
        this.customer$ = this.store.pipe(select(allCustomers));
        this.refresh();
    }
    
    addCustomer(customer: Customer) {
        this.store.dispatch(new Create(Customer, customer));
    }
    
    refresh() {
        this.store.dispatch(new LoadAll(Customer));
    }
    
    // ...
}
```
{% endcode %}

Aside from no longer having to implement your own actions, everything else should be business as usual. Your controllers, when dispatching generic actions from Auto-Entity directly, should look quite familiar. 

{% hint style="info" %}
In future versions of NgRx Auto-Entity, legacy-style action classes \(i.e. `new LoadAll(...)`\) will be supplemented by NgRx 8+ style factory functions. Instead of newing up an action, you'll be able to use an action factory pre-typed specifically to your entities: 

`loadAllCustomers()`  
`createOrder(order)`  
`selectUserByKey(userId)`

This stylistic alignment should simplify NgRx Auto-Entity usage and allow deeper integration of auto-entity generated functionality with standard NgRx code, such as reducers created with `createReducer()`. \(These changes are currently slated for version 0.6\)
{% endhint %}

### Standardized Properties

Each generic action's constructors require a standard set of parameters, and some actions may require unique parameters to perform the necessary function. Most action constructors are as follows:

```typescript
constructor(type: { new (): TModel }, public criteria?: any, correlationId?: string)
constructor(type: { new (): TModel }, public keys: any, public criteria?: any, correlationId?: string)
constructor(type: { new (): TModel }, public page: Page, public criteria?: any, correlationId?: string)
constructor(type: { new (): TModel }, public range: Range, public criteria?: any, correlationId?: string)
constructor(type: { new (): TModel }, public entity: TModel, public criteria?: any, correlationId?: string)
constructor(type: { new (): TModel }, public entities: TModel[], public criteria?: any, correlationId?: string)
```

 Note that the first parameter for every action is the **model type** itself. Also take note that every action accepts optional **custom** **criteria** as the next parameter. This same criteria is later included in the arguments for each entity service method, and is transferred from the action to the entity service method _exactly as provided_ when originally dispatching the action. 

{% hint style="success" %}
Auto-Entity version 0.5 introduces action correlation identities. Every action includes a pubic **correlationId** property. This property may be utilized in custom effects to associate initiation actions with their subsequent success/failure actions in a very exact and deterministic manner. 
{% endhint %}

