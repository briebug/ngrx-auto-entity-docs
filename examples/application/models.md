---
description: Stuff about Things
---

# Models

### Customer & Address

{% tabs %}
{% tab title="models/customer.model.ts" %}
```typescript
import { Key } from '@briebug/ngrx-auto-entity';
import { Address } from './address.model';

export class Customer {
    @Key id?: number;
    name: string;
    title: string;
    email: string;
    handles?: {
        twitter?: string;
        facebook?: string;
    }
    addressId?: number;
}
```
{% endtab %}

{% tab title="models/address.model.ts" %}
```typescript
import { Key } from '@briebug/ngrx-auto-entity';

export class Address {
    @Key id?: number;
    street1: string;
    street2: string;
    city: string;
    state: string;
    zip: string;
}
```
{% endtab %}
{% endtabs %}

### Order & Line Item

{% tabs %}
{% tab title="models/order.model.ts" %}
```typescript
import { Key } from '@briebug/ngrx-auto-entity';

export type ISODate = string; // YYYY-MM-DDTHH:mm:ss-ZZ:zz
export type Never = 'never';

export enum OrderStatus {
    PENDING = 'pending',
    ONHOLD = 'on-hold',
    PARTIAL = 'partial-fill',
    FILLED = 'filled',
    PARTSHIP = 'partial-shipped',
    SHIPPED = 'shipped',
    CLOSED = 'closed'
}

export class Order {
    @Key id?: number;
    purchaseOrderNo: string;
    status: OrderStatus;
    dateCreated: ISODate;
    dateClosed: ISODate | Never;
    history: OrderHistory[];
}

export class OrderHistory {
    dateOfAction: ISODate;
    action: string;
    newStatus: OrderStatus;
}
```
{% endtab %}

{% tab title="models/lineItem.model.ts" %}
```typescript
import { Key } from '@briebug/ngrx-auto-entity';

export class LineItem {
    @Key orderId: number;
    @Key productId: number;
    quantity: number;
    isRush: boolean;
}
```
{% endtab %}
{% endtabs %}

#### Barrel

{% code title="models/index.ts" %}
```typescript
export * from './address.model';
export * from './customer.model';
export * from './lineItem.model';
export * from './order.model';
```
{% endcode %}

