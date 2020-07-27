---
description: Lazy Boilerplate...
---

# The buildFeatureState\(\) function

The lazy loaded feature module counterpart to `buildState` is, of course, `buildFeatureState`. This function is similar to `buildState`, however it is not identical and has one nuanced change:

```typescript
function buildFeatureState<
  TState extends IEntityState<TModel>, 
  TParentState, 
  TModel
>(
  type: ITModelClass<TModel>,
  featureStateName: string,
  selectParentState: MemoizedSelector<object, TParentState>,
  extraInitialState?: any
): IModelState<TParentState, TState, TModel>;
```

Two additional arguments must be passed. The second is the name of the feature state, the same name you specify in the `createFeatureSelector` call. The third argument that must be passed to `buildFeatureState` is the feature selector for the feature state. The use case would be as follows:

{% code title="feature.state.ts" %}
```typescript
import { createFeatureSelector } from '@ngrx/store';
import { FeatureEntity } from '../models';

export interface IFeatureState {
    featureEntity: IEntityState<FeatureEntity>;
}

export const FEATURE_NAME = 'feature';
export const featureEntityState = createFeatureSelector<IFeatureState>(
    FEATURE_NAME
);
```
{% endcode %}

{% code title="featureEntity.state.ts" %}
```typescript
import { buildFeatureState } from '@briebug/ngrx-auto-entity';
import { featureEntityState, FEATURE_NAME } from './feature.state';
import { FeatureEntity } from '../models';

export const { initialState, selectors } = buildFeatureState(
    FeatureEntity,
    FEATURE_NAME,
    featureEntityState
);
```
{% endcode %}

Building feature state otherwise works the same as the standard `buildState` function does. It also provides the entityState for the feature entity, a stub reducer and a facade class. 

{% hint style="danger" %}
~~While the general functionality of `buildFeatureState` works properly at a high level, there is currently a bug in NgRx Auto-Entity that prevents it from finding the proper `Injector` for lazy-loaded features. Each lazy feature contains its own angular injector instance as well, a child injector that will fall back on the root app injector as necessary.  Until this bug is resolved, lazy loaded feature state will not properly function. Our sincerest apologies!~~

This issue is resolved with v0.2 of NgRx Auto-Entity. You should be able to build feature state with models and entity services encapsulated within lazy loaded modules! Please notify us in our GitHub issues if you encounter any problems with this feature. It is surprisingly complex!
{% endhint %}

### Differences vs. Root State

When using feature state, there are some nuanced differences that must be taken into account. With NgRx feature state becomes a new state property on the root state, with the name given to the `createFeatureState` function. This new root state property then encapsulates all other state properties for that particular feature.

```javascript
{
    routerState: { ... }, 
    customer: { ... }, // Auto-Entity in root state
    products: {
        product: { ... } // Auto-Entity in feature 'products'
    },
    orders: {
        order: { ... }, // Auto-Entity in feature 'orders'
        lineItem: { ... } // Auto-Entity in feature 'orders'
    }
}
```

Due to these differences, it becomes very important to specify the proper name for both the `createFeatureState` and the `buildFeatureState` function calls. Hence the use of a constant in the prior example.

In addition to this little nuance, there is another critical change that must be made. Where root state may easily share the state interface and reducer map in a single file, such as `app.state.ts`, due to the fact that `buildFeatureState` depends on the state created by `createFeatureState`, but the reducer map depends on the reducer that you create with the `initialState` returned by `buildFeatureState`, putting the state and the reducer in a single file results in a circular reference problem.

The solution is to simply put the feature state interface and `createFeatureState` call in one file, say `feature.state.ts`, and put the reducer map in another, say `feature.reducer.ts`. This breaks the chain and eliminates the cycle. 

