---
description: Things we Need
---

# State

### States

{% code-tabs %}
{% code-tabs-item title="state/app.state.ts" %}
```typescript
import { IEntityState } from '@briebug/ngrx-auto-entity';
import { ActionReducerMap } from '@ngrx/store';

import { Customer, Order } from '../models';
import { customerReducer } from './customer.state';
import { orderReducer } from './order.state';

export interface IAppState {
    customer: IEntityState<Customer>;
    order: IEntityState<Order>;
}

export type AppState = IAppState;

export function appReducer: ActionReducerMap<AppState> = {
    customer: customerReducer,
    order: orderReducer
};
```
{% endcode-tabs-item %}

{% code-tabs-item title="state/customer.state.ts" %}
```typescript
import { buildState, IEntityState } from '@briebug/ngrx-auto-entity';
import { createReducer } from '@ngrx/store';
import { Customer } from 'models';

export const { initialState: customerInitialState, facade: CustomerFacadeBase } = buildState(Customer);
export const customerReducer = createReducer(customerInitialState);
```
{% endcode-tabs-item %}

{% code-tabs-item title="state/order.state.ts" %}
```typescript
import { buildState, IEntityState } from '@briebug/ngrx-auto-entity';
import { createReducer } from '@ngrx/store';
import { Order } from 'models';

export const { initialState: orderInitialState, facade: OrderFacadeBase } = buildState(Order);
export const orderReducer = createReducer(initialState);
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Module

{% code-tabs %}
{% code-tabs-item title="state/state.module.ts" %}
```typescript
import { NgModule } from '@angular/core';
import { NgrxAutoEntityModule } from '@briebug/ngrx-auto-entity';
import { EffectsModule } from '@ngrx/effects';
import { StoreModule } from '@ngrx/store';

import { appReducer } from './app.state';

@NgModule({
  imports: [
    StoreModule.forRoot(appReducer, {
      runtimeChecks: {
        strictStateImmutability: true,
        strictActionImmutability: true,
        strictStateSerializability: true,
        strictActionSerializability: true,
      },
    }),
    EffectsModule.forRoot([]),
    NgrxAutoEntityModule.forRoot()
  ]
})
export class StateModule {}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### Barrel

{% code-tabs %}
{% code-tabs-item title="state/index.ts" %}
```typescript
export * from './app.state';
export * from './customer.state';
export * from './order.state';
export * from './state.module';
```
{% endcode-tabs-item %}
{% endcode-tabs %}

