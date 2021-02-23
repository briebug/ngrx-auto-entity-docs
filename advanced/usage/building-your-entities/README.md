---
description: Foundations Laid
---

# Building Your Entities

Entity models are central to Auto-Entity. These describe your data, not just in terms of the data itself, but how Auto-Entity interacts with each entity. The `@Entity` decorator allows you to configure each entity, including specifying non-minifiable entity names, comparers for sorting purposes, transforms for converting data to and from server formats, and more.  The @Key decorator allow you to mark which properties of your entities comprise the unique identifier of each. 

### The @Entity Decorator

A range of metadata may be used to configure your entities using the `@Entity` decorator. This decorator is intended to decorate the classes you use to define your entity models. The only required property is the `modelName`, which provides auto-entity with a non-minifiable name that uniquely describes the identifier of your model. A non-minifiable name \(i.e. vs. `constructor.name`\) is critical, as auto-entity relies on this name to find the slice of state for your entity and perform other internal operations. 

In addition to the model name, the `@Entity` decorator allows you to describe a default sort comparer as well as named sort comparers, compose pipelines of data transforms, define a default maximum age for entities for use with optional loading, and finally exclude the entity from certain automatic effects handling \(for very advanced use cases\). 

### The @Key Decorator

For any entity to be managed by auto-entity, it must have an identity. A unique identity. Auto-entity supports entity keys that are single-property, or multi-property. A multi-property or composite key will be handled automatically by auto-entity. There is an internal format for these that ensures they are strings that can be used as keys in the ngrx state.

{% tabs %}
{% tab title="Customer" %}
```typescript
import { Entity, Key } from '@briebug/ngrx-auto-entity';

@Entity('Customer')
export class Customer {
  @Key id: string;
  name: string;
  // ...
}
```
{% endtab %}

{% tab title="Line Item" %}
```typescript
import {Entity, Key} from '@briebug/ngrx-auto-entity';

@Entity('LineItem')
export class LineItem {
    @Key orderId: string;
    @Key productId: string;
    quantity: number;
}
```
{% endtab %}
{% endtabs %}

Entity keys may be retrieved with one of many key getter utility functions. Check the [Utility Functions](../../utilities/entity-key-utilities.md) documentation for more information. 

