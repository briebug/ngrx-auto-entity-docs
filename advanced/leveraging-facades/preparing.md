---
description: Just a dash of boilerplate...
---

# Preparing Facades

NgRx Auto-Entity will generate a new facade class for you dynamically whenever you call `buildState`. You may optionally destructure this facade class from the response, along with destructuring other core aspects of your new state:

{% code title="customer.state.ts" %}
```typescript
export const { initialState, facade: CustomerFacadeBase } = buildState(Customer);
export function customerReducer(state = initialState): IEntityState<Customer> {
    return state;
}
```
{% endcode %}

The above is the recommended and most minimal use case for the building states with Auto-Entity. For most use cases, nothing beyond the `initialState` and `facade` class should be required. 

We also recommend renaming for export the name `facade` into a more appropriate, unique name for a base facade class for your entity. In the example here, we have renamed the facade to `CustomerFacadeBase`.

### Extending a Concrete Facade

Once you have your base facade class, you will need to extend the proper facade class from it. Extension is required in order to provide the facade base class with the necessary services. Since our facades are dynamically generated classes created by a simple function, we are unable to leverage the Angular Injector directly.

{% code title="customer.facade.ts" %}
```typescript
export class CustomerFacade extends CustomerFacadeBase {
    constructor(private store: Store<AppState>) {
        super(Customer, store);
    }
}
```
{% endcode %}

The above is the most minimal use case for a facade. The only **requirement** is to call `super()` from within your constructor, and pass in the entity model type as well as the store. From here, you may start using all of the ready-made functionality the base facade offers, without any additional work. 

