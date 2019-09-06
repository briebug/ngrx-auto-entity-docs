---
description: Understand changes to model implementation
---

# Models

With NgRX Auto-Entity, a very small change must be made to how models are implemented. Standard @ngrx models are implemented as _interfaces_. A normal model used with @ngrx/entity may look like this:

{% code-tabs %}
{% code-tabs-item title="src/app/models/customer.ts" %}
```typescript
export interface Customer {
    id: number;
    name: string;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

This simple model would then be wired into @ngrx/entity using the adapter, with a selectId handler to allow entity framework to determine what the primary key of the entity is. 

### Dynamic Library

Due to the dynamic nature of how NgRX Auto-Entity works, interfaces are insufficient as they are compile-time only types in TypeScript. All of their information is ultimately stripped from the transpiled and optimized JavaScript code that you will ultimately release to a web server. To combat this issue, we must convert our entities to classes, which are a native runtime feature of JavaScript.

{% code-tabs %}
{% code-tabs-item title="src/app/models/customer.ts" %}
```typescript
import {Key} from '@briebug/ngrx-auto-entity';
import {ObjectId} from 'types';

export class Customer {
    @Key id: ObjectId;
    name: string;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Identifying Keys

In addition to converting our model from an interface to a class, notice that we have also imported `Key` from `@briebug/ngrx-auto-entity` and decorated our `id` property with `@Key`. This is how the Auto-Entity framework is able to determine which property of the model represents the primary key. As with @ngrx/entity, this key is used to organize and look up entities stored in state. 

#### Composite Keys

{% hint style="info" %}
Note that Auto-Entity supports composite keys. Simply decorate each property that participates in a primary key with the `@Key` decorator if necessary.
{% endhint %}

A simple but common example of a composite key might be something along the lines of an order line item, which references both an order and a product along with a quantity:

{% code-tabs %}
{% code-tabs-item title="src/app/models/lineItem.ts" %}
```typescript
import {Key} from '@briebug/ngrx-auto-entity';
import {ObjectId} from 'types';

export class LineItem {
    @Key orderId: ObjectId;
    @Key productId: ObjectId;
    quantity: number;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

For more detail about how composite keys work, read the advanced usage documentation on models and keys.

