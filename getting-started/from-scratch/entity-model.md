---
description: Create your entity models.
---

# Entity Model

In a departure from classic @ngrx/entity models, each model in your application should be defined as a **class** \(see note below\). ****Here is an example of a `Customer` model:

{% code title="customer.model.ts" %}
```typescript
import { Key } from '@briebug/ngrx-auto-entity';

export class Customer {
  @Key id: number;
  name: string;
  catchPhrase: string;
}
```
{% endcode %}

Next we need to import the `Key` decorator. This is used to specify the property in your model that is the unique identifier. Decorate the `id` property, which is the unique identifier for `Customer` model. Read more about entity keys in the advanced documentation.

{% hint style="warning" %}
Note that the model must be a **class** and **not an interface**. This is because interfaces are a _compile-time only_ feature of TypeScript, while classes are a native _runtime_ construct in JavaScript.
{% endhint %}

