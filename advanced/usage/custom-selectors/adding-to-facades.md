---
description: Keeping your state and store centralized
---

# Adding to Facades

If you do encounter the need to implement custom selectors, it is highly recommended that you integrate them into your entity facades, rather than consume them directly from components. This does of course assume you are using entity facades, and if you have foregone the use of facades then you may use custom selectors as you normally do with @ngrx. 

### Property or Accessor Method

After creating a custom selector, you will need to determine whether a property or accessor method is appropriate for this new selector in your facade. For non-parameterized selectors, a property is usually most appropriate. 

For parameterized selectors, an accessor method may be more appropriate. If however the possible range of input to your parameters is limited then multiple properties that each encompass a particular use case might still be the best option.

### Adding a Custom Property 

Continuing on with our `firstCustomer` selector from the previous section, adding this to a `CustomerFacade` should be very strait forward:

{% code title="customer.facade.ts" %}
```typescript
import {firstCustomer, CustomerFacadeBase} from 'state/customer.state';
import {Customer} from 'models';

export class CustomerFacade extends CustomerFacadeBase {
    firstCustomer$ = this.store.select(firstCustomer);
    
    constructor(store: Store<AppState>) {
        super(Customer, store);
    }
}
```
{% endcode %}

### Adding a Custom Accessor Method

With a parameterized selector like our prior `customerById` example, we should use a method instead of a property to allow passing in the id of the customer to select:

{% code title="customer.facade.ts" %}
```typescript
import {firstCustomer, customerByName, CustomerFacadeBase} from 'state/customer.state';
import {Customer} from 'models';

export class CustomerFacade extends CustomerFacadeBase {
    firstCustomer$ = this.store.select(firstCustomer);

    constructor(store: Store<AppState>) {
        super(Customer, store);
    }
    
    customerByName$(name: string): Observable<Customer> {
        return this.store.select(customerByName, {name});
    }
}
```
{% endcode %}

