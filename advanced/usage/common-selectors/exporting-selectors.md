# Exporting Selectors

Selectors are generated for a given entity when you call `buildState` with a model class. The selectors may be initially destructured out of the object returned by buildState. From there, you may choose to simply export the entire selectors object, or further destructure the selectors and export only those you need to use:

{% code title="customer.state.ts" %}
```typescript
import {buildState} from '@briebug/ngrx-auto-entity';
import {Customer} from 'models';

const { 
    selectors: {
        selectAll: allCustomers,
        selectCurrentEntity: currentCustomer        
    }
} = buildState(Customer);
```
{% endcode %}

### Selectivity with Selectors

It is not required to export all selectors. Through destructuring, it is recommended that you only export the selectors that you will actually use within your application. Further, we highly recommend renaming each selector to utilize the name of the model they relate to. 

At BrieBug, we have moved away from naming selectors `selectWhatever` in an attempt to maintain DRY principal:

```typescript
this.store.pipe(select(selectAllCustomers)); // Repetition of 'select' here. :(
this.store.pipe(select(allCustomers)); // No repetition, natural language flow! :)
```



