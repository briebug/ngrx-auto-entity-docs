---
description: The Beginning
---

# App Module

```typescript
import { HttpClientModule } from '@angular/common/http';
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';

import { 
  CustomersComponent, 
  CustomerListComponent,
  CustomerDetailComponent,
  OrdersComponent,
  OrderListComponent,
  OrderDetailComponent
} from 'components';
import { Customer, Address, Order, LineItem } from 'models';
import { EntityService } from 'services';
import { StateModule } from 'state';

@NgModule({
  bootstrap: [
    AppComponent
  ],
  declarations: [
    AppComponent,
    CustomersComponent,
    CustomerListComponent,
    CustomerDetailComponent,
    OrdersComponent,
    OrderListComponent,
    OrderDetailComponent
  ],
  imports: [
    BrowserModule,
    HttpClientModule,
    StateModule.forRoot(),
  ],
  providers: [
    { provide: Address, useClass: EntityService },
    { provide: Customer, useClass: EntityService },
    { provide: LineItem, useClass: EntityService },
    { provide: Order, useClass: EntityService }
  ]
})
export class AppModule {}

```

