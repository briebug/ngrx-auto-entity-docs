---
description: Identity please!
---

# Entity Key Utilities

Along with entity name utility function, NgRx Auto-Entity also provides a number of key retrieval functions. Entity keys are a first class concept in Auto-Entity, with support for string or numeric keys as well as composite keys. In many cases you may simply know the keys of your entities and may be able to use them directly. In other cases, you may be using composite keys or may be working with many dynamic entities that do not have known specifics. The entity key utility functions are available for these cases. 

### getKey

The original key retrieval utility, used in the library from its early days, is `getKey()` and is used internally for most key retrieval. This method allows you to provide an `IEntityAction` along with an entity instance, and retrieve the key of the entity. 

```typescript
const action = new LoadSuccess(Customer, customer);
const key = getKey(action, customer); 
```

Since a lot of the internal functionality of Auto-Entity has access to the actions being dispatched, this is the primary method of key retrieval by the framework itself. It may not be as useful for the end developer.

### getKeyFromModel

An additional method added in v0.2, allows retrieval of an entity's key by providing the model class and an entity instance. This is the primary use case for the end developer in most cases, as the model class should be readily available.

```typescript
const key = getKeyFromModel(Customer, customer);
```

If you need to get a key from an entity, this is the recommended approach. 

### getKeyFromEntity

A final method, added in v0.5, allows the retrieval of an entity's key by providing a properly typed \(properly prototyped\) entity instance \(i.e. via `makeEntity`\). This method is a use case if you intend to convert your entities to properly typed objects as a matter of course.

```typescript
const typedCustomer = makeCutomer(customer);
const key = getKeyFromEntity(typedCustomer);
```

