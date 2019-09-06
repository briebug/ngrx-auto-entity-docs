---
description: Create application reducer and meta reducer.
---

# App Reducer

Also like normal NgRX apps, add a reducer map to your **app.state.ts** file. We recommend creating this file in a root **state** directory, located at src/app/state.

{% code-tabs %}
{% code-tabs-item title="app.state.ts" %}
```typescript
import { ActionReducerMap, MetaReducer } from '@ngrx/store';
import { IEntityState } from '@briebug/ngrx-auto-entity';
import { environment } from '../../environments/environment';

export interface IAppState {
  // todo: add each entity state interface to our application state interface
}
export type AppState = IAppState;

export const appReducer: ActionReducerMap<AppState> = {
  // todo: add each entity reducer
};

export const appMetaReducers: Array<MetaReducer<AppState>> = 
  !environment.production ? [storeFreeze] : [];
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="danger" %}
In versions of NgRx Auto-Entity prior to v0.2, the developer was also responsible for including the `autoEntityMetaReducer` in the app meta reducers collection. As of version 0.2 of the library, import of the `NgrxAutoEntityModule` with the `.forRoot()` call is all that is necessary to include the meta reducer.

If you are upgrading from a version prior to v0.2, you should remove the `autoEntityMetaReducer` from your app meta reducers!
{% endhint %}

