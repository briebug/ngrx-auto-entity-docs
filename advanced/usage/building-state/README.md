---
description: Initialization Simplified
---

# Building Your Entity States

At the core of NgRx Auto-Entity is the `buildState` function. This utility function encapsulates the creation and management of all the "boilerplate" that you would normally implement yourself when building up state for a new entity. In the past you may have manually created an @ngrx/entity adapter, built out your standardized reducer function, and exported a bunch of support elements like initial state, selectors, a key selection handler, etc. 

### Just "buildState\(\)!"

Now you simply need to call `buildState()` like so:

{% code title="entity.state.ts" %}
```typescript
import { IEntityState } from '@briebug/ngrx-auto-entity';
import { Model } from 'models';

const { initialState } = buildState(Model);
export function entityReducer(state = initialState): IEntityState<Model> {
    return state;
}
```
{% endcode %}

That is about as simple as it gets, unless you need to do anything custom, such as add your own additional support actions, use selectors, or retrieve the prefabricated facade base class so you can keep your state encapsulated. 

### Rich Functionality

The `buildState` function provides all of the functionality necessary to take control when you need to, without being complex, and all while still handling most of the work for you. The full expression of a `buildState` call would be as follows:

```typescript
import { IEntityState } from '@briebug/ngrx-auto-entity';
import { Model } from 'models';

export const { 
    initialState, 
    selectors: {
        selectAll: allModels,
        selectEntities: modelEntities,
        selectIds: modelIds,
        selectTotal: countOfModels,
        // Additional selectors...
    }, 
    entityState, 
    facade: ModelBaseFacade,
    reducer: modelReducer // Sadly, this does not work with AOT! :'(
} = buildState(Model);

// Extracted projection for easier unit testing
export const findFirstModel = (state: IEntityState<Model>) => 
    (entityState.ids?.length && entityState.entities) 
      ? entityState.entities[entityState.ids[0]] 
      : null;

// Selectors are simply compositions of other selectors and a projection
export const firstModel = createSelector(
    entityState,
    findFirstModel
);
```

In addition to providing the initial state version for your stub reducer, the `buildState()` function also provides a `selectors` map, an `entityState` selector to support the creation of custom selectors, a base `facade` class \(source of ultimate power and simplicity!\), and finally a ready-made stub `reducer` \(only works with non-AOT builds, sorry!\)

