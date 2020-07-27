---
description: When ready-made just isn't enough
---

# Custom Selectors

While NgRx Auto-Entity provides quite a bit of ready-made functionality, tracking state for many of the most common elements of entity state including the usual "custom" aspects such as the currently selected entity, loading flags, etc. there may be cases where you still need to create a custom selector. Even when using prefabricated facades, once you get into extending those facades, custom selectors are not uncommon. 

{% code title="customer.state.ts" %}
```typescript
import { createSelector } from '@ngrx/store';
import { buildState } from '@briebug/ngrx-auto-entity';

const { 
    selectors: {
        selectIds,
        selectEntities
    }, 
    entityState
} = buildState(Customer);

export const firstCustomer = createSelector(
    selectIds,
    selectEntities,
    (ids, entities) => 
        ids?.length && entities ? entities[ids[0]] : null;
);
```
{% endcode %}

### Nothing New Here

Custom selectors are implemented as you have always implemented them. Nothing new or special there. Parameterized selectors may also still be used. Make sure to extract the `entityState` property from the return value from `buildState` if you need to access the whole state \(including all of the additional data tracked by Auto-Entity\) for a given entity.

{% hint style="info" %}
Don't forget to destructure the `selectors` map to pull out the specific selectors required for composition into custom selectors. Also remember that nested structures may be destructured inline with modern javascript as well!
{% endhint %}

### Parameterized Selectors Supported

Note that it is also possible to create parameterized selectors with @ngrx. You may still implement parameterized selectors with Auto-Entity as well:

{% code title="customer.state.ts" %}
```typescript
export const customerByName = createSelector(
    selectIds,
    selectEntities,
    (ids, entities, props) => 
        ids.map(id => entities[id])
           .find(entity => entity.name === props.name);
);
```
{% endcode %}

Follow the necessary precautions and best practices outlined in the ngrx.io documentation when using parameterized selectors. There are some important caveats to be aware of with their use. 

