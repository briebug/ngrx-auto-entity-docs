---
description: Business Central
---

# Facades

### Customer Facade

{% code-tabs %}
{% code-tabs-item title="facades/customer.facade.ts" %}
```typescript
import { Injectable } from '@angular/core';
import { store } from '@ngrx/store';

import { CustomerEditComponent } from 'components';
import { Customer } from 'models';
import { AppState, CustomerFacadeBase, firstCustomer } from 'state'

@Injectable({ providedIn: 'root' })
export class CustomerFacade extends CustomerFacadeBase {
    constructor(store: Store<AppState>, private modal: Modal) {
        super(Customer, store);
    }
    
    get first$(): Observable<Customer> {
        return this.store.pipe(select(firstCustomer));
    }
    
    get currentOrFirst$(): Observable<Customer> {
        return combineLatest(this.current$, this.first$).pipe(
            map(([current, first]) => current || first)
        );
    }
    
    save(customer: Customer): void {
        customer.id ? this.update(customer) : this.create(customer);
    }
}
```
{% endcode-tabs-item %}

{% code-tabs-item title="facades/customer-ui.facade.ts" %}
```typescript
import { Injectable } from '@angular/core';

import { CustomerEditComponent } from '../components';
import { Customer } from '../models';

@Injectable({ providedIn: 'root' })
export class CustomerUIFacade {
    constructor(private modal: Modal, private customerFacade: CustomerFacade) {
    }
    
    edit(customer: Customer): void {
        const reference = this.modal.show(CustomerEditComponent);
        reference.dismissed(editedCustomer => {
          this.customerFacade.save(editedCustomer);
        });
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="facades/order.facade.ts" %}
```typescript
import { Injectable } from '@angular/core';
import { store } from '@ngrx/store';

import { Order } from 'models';
import { AppState, OrderFacadeBase } from 'state'

@Injectable({ providedIn: 'root' })
export class OrderFacade extends OrderFacadeBase {
    constructor(store: Store<AppState>, private modal: Modal) {
        super(Order, store);
    }
    
    save(order: Order): void {
        order.id ? this.update(order) : this.create(order);
    }
}
```
{% endcode-tabs-item %}

{% code-tabs-item title="facades/lineItem.facade.ts" %}
```typescript
import { Injectable } from '@angular/core';
import { store } from '@ngrx/store';

import { LineItem } from 'models';
import { AppState, LineItemFacadeBase } from 'state'

@Injectable({ providedIn: 'root' })
export class LineItemFacade extends LineItemFacadeBase {
    constructor(store: Store<AppState>, private modal: Modal) {
        super(LineItem, store);
    }
    
    byOrder$(order: Order): Observable<LineItem[]> {
        return this.all$.pipe(
            map(lineItems => 
                lineItems.filter(lineItem => lineItem.orderId === order.id)
            )
        );
    }
    
    deleteAllForOrder(order: Order): void {
        this.byOrder$(order).pipe(
            tap(lineItems => this.deleteMany(lineItems))
        );
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="facades/index.ts" %}
```typescript
export * from './customer.facade';
export * from './customer-ui.facade';
```
{% endcode-tabs-item %}
{% endcode-tabs %}

