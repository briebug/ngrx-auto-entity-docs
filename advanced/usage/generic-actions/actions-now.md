---
description: Current Complexity with Action Definitions
---

# Actions Now

A standard approach to implementing actions with @ngrx requires defining an enumeration of action types, which map a code identifier to a string, implementation of an action class that derives from `Action` and the concatenation of each action type into an action union that allows proper implementation of a reducer function to reduce actions and the information they contain into your state. 

{% code-tabs %}
{% code-tabs-item title="customer.actions.ts" %}
```typescript
export enum CustomerActionTypes {
    CREATE_CUSTOMER = '[Customer] Create',
    CREATE_CUSTOMER_SUCCESS = '[Customer] Create: Success',
    CREATE_CUSTOMER_FAILURE = '[Customer] Create: Failure',
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
    
    constructor(public payload: Error) {}
}

// ... additional actions ...

export union CustomerActions = 
      CreateCustomer
    | CreateCustomerSuccess
    | CreateCustomerFailure 
     // ... additional actions to union ...;
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Lot of work!

This is a lot of work required just to add the ability to create an entity. Not only do you need the ability to handle the initial create request, but also deal with the success or failure of that request, at the very least. So each "action" generally tends to trifurcate, and thus the action types and action union code trifurcates as well. 

{% hint style="info" %}
Note: The most recent release of NgRx, version 8, has introduced some utility functions that can help reduce some of the "boilerplate" nature of implementing actions, effects and reducers. These new functions are a welcome improvement over prior versions of NgRx...however, we do believe they still fall short of providing the kind of simplified, rapid development experience Auto-Entity provides. 
{% endhint %}

### Dispatched from Components

Once actions are defined, one may then dispatch them using the @ngrx store. This is usually done within Angular container components:

{% code-tabs %}
{% code-tabs-item title="customers.component.ts" %}
```typescript
import {Customer} from 'models';
import {CreateCustomer, LoadCustomers} from 'state/customer.actions';
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
        this.store.dispatch(new CreateCustomer(customer));
    }
    
    refresh() {
        this.store.dispatch(new LoadCustomers();
    }
    
    // ...
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

For actions to actually do anything, however, you need more. Your work does not end here. You still need effects, and reducers, to make any of these dispatched actions actually **perform useful work** and **update state**. 

