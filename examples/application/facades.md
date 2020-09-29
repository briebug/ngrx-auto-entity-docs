---
description: Business Central
---

# Facades

### Customer Facade

{% tabs %}
{% tab title="facades/customer.facade.ts" %}
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
        return this.store.select(firstCustomer);
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
{% endtab %}

{% tab title="facades/customer-ui.facade.ts" %}
```typescript
import { Injectable } from '@angular/core';

import { CustomerEditComponent } from '../components';
import { Customer } from '../models';

@Injectable({ providedIn: 'root' })
export class CustomerUIFacade {
    constructor(private modals: Modal, private customerFacade: CustomerFacade) {
    }
    
    async edit(customer: Customer): Promise<void> {
        const modal = this.modals.show(CustomerEditComponent);
        const editedCustomer = await modal.dismissed();
        this.customerFacade.save(editedCustomer);
    }
}
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="facades/order.facade.ts" %}
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
{% endtab %}

{% tab title="facades/lineItem.facade.ts" %}
```typescript
import { Injectable } from '@angular/core';
import { store } from '@ngrx/store';

import { LineItem } from 'models';
import { AppState, LineItemFacadeBase } from 'state'

export const lineItemsByOrder = createSelector(
    allLineItems,
    (lineItems, {order}) => 
      lineItems.filter(lineItem => lineItem.orderId === order.id)
)

@Injectable({ providedIn: 'root' })
export class LineItemFacade extends LineItemFacadeBase {
    constructor(store: Store<AppState>, private modal: Modal) {
        super(LineItem, store);
    }
    
    byOrder$(order: Order): Observable<LineItem[]> {
        return this.store.select(lineItemsByOrder, {order})
    }
    
    deleteAllForOrder(order: Order): void {
        this.byOrder$(order).pipe(
            take(1),
            tap(lineItems => this.deleteMany(lineItems))
        ).subscribe();
    }
}
```
{% endtab %}
{% endtabs %}

