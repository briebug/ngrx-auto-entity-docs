# Entity Making Performance

For those concerned about performance, there is a performance hit to making POJOs into properly prototyped objects. It is not a free process, however NgRx Auto-Entity utilizes the fastest known mechanism at the moment in order to create an object with the right prototype and properties. 

### Create & Assign

The current approach used to create an object with the proper prototype and the same properties and data as the reference object utilizes the Object.create and Object.assign functions from standard  ECMAScript. There are other ways to attach a prototype, however they are not ideal due to performance concerns.

The current implementation of `makeEntity` is as follows:

```typescript
export const makeEntity = <TModel>(Type: TNew<TModel>) => obj =>
  obj && Type && Object.assign(Object.create(Type.prototype), obj);
```

This creates a new object that uses the same prototype as your entity model class. Once that object is created, the properties of your supplied reference object are assigned to the newly created and properly prototyped object.

Under basic testing, \`makeEntity\` will convert 100,000 POJOs into prototyped objects in about 60-70 milliseconds. It's performance impact is not particularly significant, however it does add overhead. As such, currently in v0.5.x, we are not converting entities implicitly internal to Auto-Entity. We continue to leverage the model type for metadata. We may provide a configurable option in a future version of Auto-Entity to force conversion of all entities that are touched by any auto-entity library code, to ensure that entities that are retrieved from auto-entity state are always properly prototyped. 

#### Object.assign

You may wonder why `Object.assign` rather than an object spread `{...obj}`. Wouldn't a spread do the same thing? Not quite. While in many circumstances, the result of performing an object spread and Object.assign are similar, there is a nuanced difference: Object.assign **sets** properties, while the spread operator **defines** properties.

The spread operator is inherently a creation operator, creating everything as it works through the root level of the object being spread. The assign function on the other hand will set each property on the target object. If the target has existing properties defined for a given property name, then the setters of those properties will be called during the assignment. If you have created rich model types with custom property definitions, then Object.assign is the only way to properly convert a simple POJO into a proper entity that conforms to all of the rules of your model type.

### Shallow

It should also be noted that `makeEntity` is a shallow operation. Since the `@Entity` decorator only applies to the root model, there is currently no need to perform a deep clone. As such, the performance impact of making an entity from a POJO is only one level deep.

## Why not setObjectPrototype?

You may wonder why we do not use the `setObjectPrototype` function on `Object` given that it is a standard function and has been supported by browsers for some time now. Sadly, this function has known performance limitations, limitations that are not restricted to just the execution of the initial call. 

According to the [MDN documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf#:~:text=The%20Object.setPrototypeOf%20%28%29%20method%20sets%20the%20prototype%20%28i.e.%2C,slow%20operation%20in%20every%20browser%20and%20JavaScript%20engine.) on `setObjectPrototype`, the effects of reassigning the prototype of an object may persist on that object instance for its entire lifetime, and affect every piece of code that has access to the object. While simply reassigning the prototype of an existing object would seem more ideal than creating a new object with the proper prototype, until such time as this function is implemented in a highly efficient manner...and more specifically a manner that does not impose secondary performance concerns...we will not be using it within NgRx Auto-Entity. 



