---
description: Understand the changes to defining service providers
---

# Service Providers

For most Angular applications, services can be provided very simply, simply by including the class in the array of `providers` in your module. With NgRX Auto-Entity, due to its dynamic nature we must change how services for entities are provided a little bit. 

### Mapping Model to Service

Instead of registering the service itself directly, we must instead register the **model class** as the provider, and map it to an entity service via the `useClass` option. Like so:

{% code title="src/app/app.module.ts" %}
```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { NgrxAutoEntityModule } from '@briebug/ngrx-auto-entity';

import { AppComponent } from './app.component';

import { Customer } from 'models';
import { CustomerService } from 'services/customer.service';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, NgrxAutoEntityModule],
  providers: [
    { provide: Customer, useClass: CustomerService }
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}
```
{% endcode %}

When dispatching an action, the model class is specified as the first parameter, and as such the model class is the only thing NgRX Auto-Entity can use to look up the necessary service provider. By mapping the model class to the service class, you are leveraging a standard Angular paradigm to support dynamic service lookup at runtime.

### Efficiency with Service Mappings

Auto-Entity makes it possible to reuse a single smart entity service with multiple entities. This approach allows another reduction in developer effort, requiring an initial effort up front to implement the entity service, however over the lifetime of the application that single service may be used countless times for countless entities.

Mapping each entity to a service may not be the most efficient approach. Each provider will be a new instance of the entity service. In order to supply a single service instance you can use a factory with another provider.

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { NgrxAutoEntityModule } from '@briebug/ngrx-auto-entity';

import { AppComponent } from './app.component';

import { Customer } from 'models';
import { Product } from 'models';
import { Order } from 'models';
import { LineItem } from 'models';
import { EntityService } from 'services/entity.service';

export function provideEntityService(service: EntityService) {
  return service;
}

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, NgrxAutoEntityModule],
  providers: [
    EntityService,
    { provide: Customer, useFactory: provideEntityService, deps: [EntityService] },
    { provide: Product, useFactory: provideEntityService, deps: [EntityService] },
    { provide: Order, useFactory: provideEntityService, deps: [EntityService] },
    { provide: LineItem, useFactory: provideEntityService, deps: [EntityService] },    
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

### Minification and Service Lookup

To ensure that auto-entity's service lookup for a given identifier of application state will work even when your code has been minified/uglified, run through AoT, optimized, etc. we require that a string name for each model be defined with the `Entity` decorator. This name is used instead of the actual runtime class name, thus guaranteeing that even if the class name \(and therefor _**constructor.name**_\) is changed during minification, auto-entity will still function properly. 

{% hint style="danger" %}
NgRx Auto-Entity will still attempt to utilize the model class's constructor.name in the event that a model has not been decorated with the @Entity decorator. This can allow simple, rapid prototyping of an auto-entity application when run in development mode. However without proper decoration, minified, optimized, aot production code will not function properly. 

A future version of NgRx Auto-Entity may change this behavior in favor of requiring that every entity always be decorated with @Entity. As such, it is encouraged that all entity models always be properly decorated at all times. 
{% endhint %}

