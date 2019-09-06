---
description: Understand the changes to defining service providers
---

# Service Providers

For most Angular applications, services can be provided very simply, simply by including the class in the array of `providers` in your module. With NgRX Auto-Entity, due to its dynamic nature we must change how services for entities are provided a little bit. 

### Mapping Model to Service

Instead of registering the service itself directly, we must instead register the **model class** as the provider, and map it to an entity service via the `useClass` option. Like so:

{% code-tabs %}
{% code-tabs-item title="src/app/app.module.ts" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

When dispatching an action, the model class is specified as the first parameter, and as such the model class is the only thing NgRX Auto-Entity can use to look up the necessary service provider. By mapping the model class to the service class, you are leveraging a standard Angular paradigm to support dynamic service lookup at runtime.

