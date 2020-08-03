---
description: Current Complexity with Action Definitions
---

# Actions Now

A standard approach to implementing actions with @ngrx requires defining an enumeration of action types, which map a code identifier to a string, implementation of an action class that derives from `Action` and the concatenation of each action type into an action union that allows proper implementation of a reducer function to reduce actions and the information they contain into your state. 

{% code title="customer.actions.ts" %}
```typescript
export enum CustomerActionTypes {
    CREATE_CUSTOMER = '[Customer] Create',
    CREATE_CUSTOMER_SUCCESS = '[Customer] Create: Success',
    CREATE_CUSTOMER_FAILURE = '[Customer] Create: Failure',
    LOAD_ALL_CUSTOMERS = '[Customer] Load: All',
    // ... additional types ...
}

export class CreateCustomer implements Action {
    readonly type = CustomerActionTypes.CREATE_CUSTOMER;
    
    constructor(public payload: Customer) {}
}

export class CreateCustomerSuccess implements Action {
    readonly type = CustomerActionTypes.CREATE_CUSTOMER_SUCCESS;
    
    constructor(public payload: Customer) {}
}

export class CreateCustomerFailure implements Action {
    readonly type = CustomerActionTypes.CREATE_CUSTOMER_FAILURE;
    
    constructor(public payload: Error | any) {}
}

export class LoadAllCustomers implements Action {
    readonly type = CustomerActionTypes.LOAD_ALL_CUSTOMERS;
}

// ... additional actions ...

export union CustomerActions = 
      CreateCustomer
    | CreateCustomerSuccess
    | CreateCustomerFailure 
    | LoadAllCustomers
     // ... additional actions to union ...;
```
{% endcode %}

#### Lot of work!

This is a lot of work required just to add the ability to create an entity. Not only do you need the ability to handle the initial create request, but also deal with the success or failure of that request, at the very least. So each "action" generally tends to trifurcate, and thus the action types and action union code trifurcates as well. 

### The createAction Factory

With the release of NgRx 8, several utility functions, factory functions, were introduced and can help reduce some of the "boilerplate" nature of implementing actions, effects and reducers. These new functions are a welcome improvement over prior versions of NgRx...however, we do believe they still fall short of providing the kind of simplified, rapid development experience Auto-Entity provides for entity use cases. 

{% hint style="info" %}
For custom use cases, we are in fact huge **fans** of the new factory functions in NgRx. They provide a much cleaner approach to using NgRx in general, for actions, effects, etc. Even with automatic entities, applications that fully rely on NgRx for the bulk of the application logic will require many additional actions, effects, selectors, reducers, etc. We _**strongly**_ encourage their use whenever you do not need boilerplate entity support!
{% endhint %}

Using the `createAction` factory, we can reduce the previous boilerplate to the following:

```typescript
export const createCustomer = createAction(
    '[Customer] Create',
    props<{customer: Customer}>()
);

export const createCustomerSuccess = createAction(
    '[Customer] Create: Success',
    props<{customer: Customer}>()
); 

export const createCustomerFailure = createAction(
    '[Customer] Create: Failure',
    props<{error: Error | any}>()
);

export const loadAllCustomers = createAction(
    '[Customer] Load: All'
);
```

A definite reduction in complexity, and a small reduction in overall code volume. However, actions must still be created in order to handle entity loading/success/failure in NgRx. And, similar sets of actions must be created for each and every entity you need to use in your app.

### Dispatched from Components

Once actions are defined, one may then dispatch them using the @ngrx store. This is usually done within Angular container components:

{% code title="customers.component.ts" %}
```typescript
import {Customer} from 'models';
import {createCustomer, loadAllCustomers} from 'state/customer.actions';
import {allCustomers} from 'state/customer.selectors';

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
        this.store.dispatch(createCustomer({customer}));
    }
    
    refresh() {
        this.store.dispatch(loadAllCustomers());
    }
    
    // ...
}
```
{% endcode %}

For actions to actually do anything, however, you need more. Your work does not end here. You still need effects, and reducers, to make any of these dispatched actions actually **perform useful work** and **update state**. 

