---
description: Maker of Entities!!
---

# Prototyping your Entities

If you have read through the previous documentation, or used NgRx Auto-Entity for some time, you may have noticed the prevalence of the model type in method and function signatures throughout the library. This requirement on the model type \(the class you define your entities with, the class you decorate with \`@Entity\`\) is to ensure that the necessary metadata required for auto-entity to do its automatic magic is available.

### Model Type

Due to the lack of automatic typing, NgRx Auto-Entity requires, in most of its functions, methods and constructors, the model type itself \(the class, not objects created from the class\) to be provided. This ensures that Auto-Entity has access to the necessary metadata. Any time you need to retrieve the metadata for an entity, you can always find a function that accepts the model type, and possibly an entity instance, to retrieve that metadata, or some aspect of it \(i.e. such as the entity key\).

When creating an entity _**instance**_ you will rarely be creating them from the model type itself. Instead, you will most likely create simple, plain old javascript objects, or POJOs for short. Something like this:

```typescript
const customer = {
  name: 'John Doe',
  email: 'john.doe@doesn-exist.com',
  address: {
    street1: '123 A Street',
    city: 'A City',
    state: 'OO',
    zip: 12345
  }
};
```

Such an object is prototyped only by `Object` itself, not your custom entity model type. If you were to try and use this model with any function or method in Auto-Entity that requires entity metadata \(provided by the `@Entity` and `@Key` decorators\), you would sadly discover that no such metadata exists. Not on your POJO object instance, anyway. 

### makeEntity\(\)

Not to fret, we have a solution to this small little problem. If you wish to make sure your entities are properly prototyped by your model types, you can call the `makeEntity` function. This function simply converts a POJO into a properly prototyped entity.

The \`makeEntity\` function is a _**curried**_ function. This means, generally speaking, that it is a function who's arguments have been broken into subsequent function calls. It is also a higher order function, in that the outer function returns another function. 

### make\[Model\]\(\)

The outer function requires the model type as a parameter. It then returns another function that can make entities of that type. The `makeEntity` function is intended to be used in a particular manner, like so:

```typescript
const makeCustomer = makeEntity(Customer);

const customer1 = { ... };
const customer2 = { ... };
const customer1Entity = makeCustomer(customer1);
const customer2Entity = makeCustomer(customer2);
```

This approach allows you to create a maker function for specific entity types, and reuse those functions over and over within your application.

### From buildState\(\)

NgRx Auto-Entity actually makes it easier for you. Whenever you call `buildState()`, an entity maker function is created for you. You simply need to destructure it from the standard result: 

```typescript
const {
  // ...
  makeEntity: makeCustomer
} = buildState(Customer);
```







