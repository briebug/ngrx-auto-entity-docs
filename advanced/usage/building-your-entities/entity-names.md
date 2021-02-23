# Entity Names

In addition to the `modelName`, the `@Entity` decorator supports other names. These include the `pluralName` which can be useful for dynamically describing entities in plurality in log messages, messages displayed in the ui, etc. Additionally a `uriName` may be specified that can be used when building API endpoint urls in your entity services. 

Some examples of using the `@Entity` decorator to name your entities:

{% tabs %}
{% tab title="Customer" %}
```typescript
import { Entity } from '@briebug/ngrx-auto-entity';

@Entity('Customer')
@Entity({ modelName: 'Customer' })
@Entity('Customer', { pluralName: 'Customers' })
@Entity({ 
  modelName: 'Customer',
  pluralName: 'Customers',
  uriName: 'customers'
})
export class Customer {}

```
{% endtab %}

{% tab title="Line Item" %}
```typescript
import { Entity } from '@briebug/ngrx-auto-entity';

@Entity({ modelName: 'LineItem' })
@Entity('LineItem', { pluralName: 'LineItems' })
@Entity({ 
  modelName: 'LineItem',
  pluralName: 'LineItems',
  uriName: 'line-item'
})
export class LineItem {}
```
{% endtab %}
{% endtabs %}

Auto-entity provides a number of utility functions for retrieving the various names of entities. Refer to the [Utility Functions](../../utilities/entity-name-utilities.md) documentation for more information.

{% hint style="warning" %}
Note that each entity class may only be decorated by the `@Entity` decorator once! The above multiple uses is only for example's sake. 
{% endhint %}

