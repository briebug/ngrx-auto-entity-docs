---
description: Understand changes to model implementation
---

# Models

With NgRX Auto-Entity, a very small change must be made to how models are implemented. Standard @ngrx models are implemented as _interfaces_. A normal model used with @ngrx/entity may look like this:

{% code title="src/app/models/customer.ts" %}
```typescript
export interface Customer {
    id: number;
    name: string;
}
```
{% endcode %}

This simple model would then be wired into @ngrx/entity using the adapter, with a selectId handler to allow entity framework to determine what the primary key of the entity is. 

### Dynamic Library

Due to the dynamic nature of how NgRX Auto-Entity works, interfaces are insufficient as they are compile-time only types in TypeScript. All of their information is ultimately stripped from the transpiled and optimized JavaScript code that you will ultimately release to a web server. To combat this issue, we must convert our entities to classes, which are a native runtime feature of JavaScript.

{% code title="src/app/models/customer.ts" %}
```typescript
import {Entity, Key} from '@briebug/ngrx-auto-entity';

@Entity({
    modelName: 'Customer',
    uriName: 'customers'
})
export class Customer {
    @Key id: string;
    name: string;
}
```
{% endcode %}

### Identifying Keys

In addition to converting our model from an interface to a class, notice that we have also imported `Key` from `@briebug/ngrx-auto-entity` and decorated our `id` property with `@Key`. This is how the Auto-Entity framework is able to determine which property of the model represents the primary key. As with @ngrx/entity, this key is used to organize and look up entities stored in state. 

#### Composite Keys

{% hint style="info" %}
Note that Auto-Entity supports composite keys. Simply decorate each property that participates in a primary key with the `@Key` decorator if necessary.
{% endhint %}

A simple but common example of a composite key might be something along the lines of an order line item, which references both an order and a product along with a quantity:

{% code title="src/app/models/lineItem.ts" %}
```typescript
import {Key} from '@briebug/ngrx-auto-entity';

export class LineItem {
    @Key orderId: string;
    @Key productId: string;
    quantity: number;
}
```
{% endcode %}

For more detail about how composite keys work, read the advanced usage documentation on models and keys.  


#### Entity Metadata

You should also notice that we have decorated our entity with the `Entity` decorator, imported from `@briebug/ngrx-auto-entity`. This decorator allows us to associate important metadata with each of our entities. Currently, the only required metadata is the `modelName` which allows us to define a minification/uglification-safe identifier for our entity models. 

The `Entity` decorator allows the definition of other metadata for our entities, including alternative names for specific use cases, a sort comparer, data transformations, and other things. For more detail about entity metadata, read the advanced usage documentation on models.

