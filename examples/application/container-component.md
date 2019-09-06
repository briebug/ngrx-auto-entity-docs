---
description: Bundles of UI
---

# Container Components

### Customers

{% code-tabs %}
{% code-tabs-item title="components/customers.component.ts" %}
```typescript
import { Component } from '@angular/core';
import { CustomerFacade } from 'facades';
import { CustomerEditComponent } from 'components';

@Component({
  selector: 'app-customers',
  templateUrl: './customers.component.html',
  styleUrls: ['./customers.component.css']
})
export class CustomersComponent {
  constructor(public customers: CustomerFacade, public ui: CustomerUIFacade) {
    customers.loadAll();
  }
}
```
{% endcode-tabs-item %}

{% code-tabs-item title="components/customers.component.html" %}
```markup
<div class="customers" *ngIf="customers.isLoading$ | async; else loading">
    <app-customer-list 
        [customers]="customers.all$ | async" 
        (deleted)="customers.delete($event)"
        (selected)="customers.select($event)">
    </app-customer-list>
    <app-customer-detail 
        *ngIf="(customers.currentOrFirst$ | async) as customer"
        [customer]="customer"
        (deleted)="customers.delete($event)"
        (edited)="ui.edit($event)">
    </app-customer-detail>
</div>
<ng-template #loading>
    Loading customers...
</ng-template>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Orders

{% code-tabs %}
{% code-tabs-item title="components/orders.component.ts" %}
```typescript
import { Component } from '@angular/core';
import { Router } froj '@angular/router';
import { OrderFacade, LineItemFacade } from 'facades';

@Component({
  selector: 'app-orders',
  templateUrl: './orders.component.html',
  styleUrls: ['./orders.component.css']
})
export class CustomersComponent {
  constructor(
    public orders: OrderFacade, 
    public lineItems: LineItemFacade,
    public router: Router) {
    orders.loadAll();
  }
}
```
{% endcode-tabs-item %}

{% code-tabs-item title="components/orders.component.html" %}
```markup
<div class="orders" *ngIf="orders.isLoading$ | async; else loading">
    <app-order-list 
        [orders]="orders.all$ | async" 
        (deleted)="orders.delete($event)"
        (selected)="orders.select($event)">
    </app-order-list>
    <app-order-detail 
        *ngIf="(orders.current$ | async) as order"
        [order]="order"
        [lineItems]="lineItems.byOrder$(order) | async"
        (deleted)="orders.delete($event); lineItems.deleteAllForOrder($event)"
        (edited)="router.navigate(['edit', $event.id])">
    </app-order-detail>
</div>
<ng-template #loading>
    Loading orders...
</ng-template>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

