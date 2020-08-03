---
description: Things we Need
---

# State

## States

{% tabs %}
{% tab title="state/app.state.ts" %}
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
{% endtab %}

{% tab title="state/customer.state.ts" %}
```typescript
import { buildState, IEntityState } from '@briebug/ngrx-auto-entity';
import { createReducer } from '@ngrx/store';
import { Customer } from 'models';

export const { initialState: customerInitialState, facade: CustomerFacadeBase } = buildState(Customer);

export function customerReducer(state = initialState): IEntityState<Customer> {
  return state;
}
```
{% endtab %}

{% tab title="state/order.state.ts" %}
```typescript
import { buildState, IEntityState } from '@briebug/ngrx-auto-entity';
import { createReducer } from '@ngrx/store';
import { Order } from 'models';

export const { initialState: orderInitialState, facade: OrderFacadeBase } = buildState(Order);

export function orderReducer(state = initialState): IEntityState<Order> {
  return state;
}
```
{% endtab %}
{% endtabs %}

## Module

{% code title="state/state.module.ts" %}
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
{% endcode %}

### Barrel

{% code title="state/index.ts" %}
```typescript
export * from './app.state';
export * from './customer.state';
export * from './order.state';
export * from './state.module';
```
{% endcode %}

