---
description: Getting started with NgRx Auto-Entity is easy!
---

# Quick Start

If you are already familiar with NgRx, then adding Auto-Entity to your Angular application is very easy. There are four major steps required to add the module, create new entity state, and provide your entity services. 

### Step 1: Importing the Module

First things first, you must bring in the `NgrxAutoEntityModule` into your app module and call the `forRoot()` method to configure the library.

```typescript
import { NgrxAutoEntityModule } from '@briebug/ngrx-auto-entity';
// ... other imports ...

@NgModule({
    imports: [
        BrowserModule,
        StoreModule.forRoot(appReducer, { metaReducers: appMetaReducers }),
        EffectsModule.forRoot([]),
        NgrxAutoEntityModule.forRoot() // Add this!
    ]
})
export class AppModule {}
```

### Step 2: Creating your Model and Entity Service

#### Entity Models

Before you can actually create your state, you will need to create your entity models, as you would normally do. Auto-Entity requires two small changes to how you create models for NgRx.

{% code-tabs %}
{% code-tabs-item title="models/customer.model.ts" %}
```typescript
import { Key } from '@briebug/ngrx-auto-entity';

export class Customer {
    @Key id: number;
    name: string;
    address: Address;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

First, your models must be classes rather than interfaces \(see [advanced documentation](../advanced/usage/paradigm/models.md) for more info.\) Second, your entity identity must be decorated with the `@Key` directive. For entities with composite keys, simply decorate each property that is part of the key. 

#### Entity Services

You will also need to create an entity service to handle CRUD behavior for each entity. Entity services may be shared if your API uses a common pattern. Otherwise you may need to implement a service for each entity as you usually do with NgRx.

{% code-tabs %}
{% code-tabs-item title="services/entity.service.ts" %}
```typescript
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

import { IAutoEntityService, IEntityInfo } from '@briebug/ngrx-auto-entity';
import { environment } from '../../environments/environment';

@Injectable()
export class EntityService implements IAutoEntityService<any> {
  constructor(private http: HttpClient) {
  }

  load(entityInfo: IEntityInfo, id: any): Observable<any> {
    return this.http.get<any>(
      `${environment.rootUrl}/${entityInfo.modelName}/${id}`
    );
  }

  loadAll(entityInfo: IEntityInfo): Observable<any[]> {
    return this.http.get<any[]>(
      `${environment.rootUrl}/${entityInfo.modelName}`
    );
  }

  create(entityInfo: IEntityInfo, entity: any): Observable<any> {
    return this.http.post<any>(
      `${environment.rootUrl}/${entityInfo.modelName}`, 
      entity
    );
  }

  update(entityInfo: IEntityInfo, entity: any): Observable<any> {
    return this.http.patch<any>(
      `${environment.rootUrl}/${entityInfo.modelName}/${entity.id}`,
       entity
    );
  }

  delete(entityInfo: IEntityInfo, entity: any): Observable<any> {
    return this.http.delete<any>(
      `${environment.rootUrl}/${entityInfo.modelName}/${entity.id}`
    ).pipe(map(() => entity));
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

In the example above we have a simple shared entity service that supports a basic REST API where each entity conforms to a simple pattern:

`/<rootUrl>/<entityName>[/<key>]`

Auto-Entity provides basic entity metadata, such as the model name, in the `entityInfo` parameter of each entity service method.

### Step 3: Providing your Services

Once you have created an entity service or services, you will need to provide them in your app module. With Auto-Entity, services must be provided in a slightly different manner than normal, to ensure that Auto-Entity is able to find entity services dynamically.

{% code-tabs %}
{% code-tabs-item title="app.module.ts" %}
```typescript
import { NgrxAutoEntityModule } from '@briebug/ngrx-auto-entity';
import { Customer, Order } from './models';
import { EntityService } from './services';
// ... other imports ...

@NgModule({
  imports: [
    BrowserModule,
    StoreModule.forRoot(...),
    EffectsModule.forRoot(...),
    NgrxAutoEntityModule.forRoot()
  ],
  // Add a provider for each model, mapping to the relevant entity service:
  providers: [
    { provide: Customer, useClass: EntityService },
    { provide: Order, useClass: EntityService }
  ]
})
export class AppModule {}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

In our example here, we are sharing a single entity service, `EntityService`, for all entities. We must `provide` each model and `useClass` to specify the service class to use. 

### Step 4: Adding your States

Finally, now that you have your models and have provided your entity services, you need to build your state for each model. Add a new state file for each model following the pattern depicted here:

{% code-tabs %}
{% code-tabs-item title="state/customer.state.ts" %}
```typescript
import { buildState, IEntityState } from '@briebug/ngrx-auto-entity';
import { Customer } from '../models'

export const { initialState, facade: CustomerFacadeBase } = buildState(Customer);

// A "stub" reducer is required to support AOT
export function customerReducer(state = initialState): IEntityState<Customer> {
  return state;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Finally, include your entity states in the AppState interface, and your stub reducers in the action reducer map:

{% code-tabs %}
{% code-tabs-item title="state/app.state.ts" %}
```typescript
import { IEntityState } from '@briebug/ngrx-auto-entity';
import { Customer, Order } from 'models';
import { customerReducer } from './customer.state.ts'
import { orderReducer } from './order.state.ts'

export interface IAppState {
  // ... other states ...
  customer: IEntityState<Customer>;
  order: IEntityState<Order>;
}
export type AppState = IAppState;

export const appReducer: ActionReducerMap<AppState> = {
  // ... other reducers ...
  customer: customerReducer
  order: orderReducer
};
```
{% endcode-tabs-item %}
{% endcode-tabs %}

With that, you are ready to start using your automatic entity state! Continue on to the next section to learn how.

