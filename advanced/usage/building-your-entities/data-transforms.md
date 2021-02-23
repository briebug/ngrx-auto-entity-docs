# Data Transforms

One of the most powerful features that Auto-Entity provides is data transforms. The data transformation model in auto-entity allows simple types that expose two functions to be composed together in a simple manner to create rich, powerful transformations of entities as they arrive from the server, or are sent to the server. Transforms can solve a multitude of problems that can often be challenging to resolve in other ways. 

### Anatomy of a Transform

A transform in auto-entity is a simple object that contains two properties, `fromServer` and `toServer`. Each of these properties must return a function that can be used to apply the transform to data going in the specified direction. A simple example:

```typescript
export const doubleName = {
  fromServer: data => (
    data.name = data.name ? `${data.name} ${data.name}` : data.name, 
    data
  ),
  toServer: entity => (
    entity.name = entity?.name.split(' ')[0],
    entity
  )
};
```

This simple object and its functions perform the very simple task of doubling a name when an entity is retrieved from a server, then un-doubling it when it is sent back to the server. The functions can be extremely basic, in this case they leverage a JavaScript comma expression to assign a property then return the updated object back without requiring any unnecessary verbosity.

#### "Non-Pure", yet Still Observationally Pure!

You may notice that the functions in the previous example do not appear to be pure. They are modifying the object passed in! That may seem as though it violates immutability, however it helps to understand the context within which these functions are called. Transformation occurs BETWEEN the entity services, and the effects that handle auto-entity actions. The transformation code will always clone your entity first before applying each configured transformation in-order.

This ensures that whenever an entity is transformed, the process IS immutable. Further, all the apparent non-purity is tightly contained within a specific function of the transformation process, and therefor _**no** mutations_ are actually **observable** to anything other than the transformation process itself. Such "mutative contexts" are often allowed in functional languages that otherwise require immutability. If mutations cannot be observed, then they cannot incur unexpected side effects. No transformation mutation can ever be observed outside of the transformation process itself.

#### Designed for Performance

The key reason why mutations \(of the clone of the entity provided by the internal transformation process\) are allowed during transformations is to ensure they are performant. If every entity in a retrieved data set required the entity to be cloned in each and every transform, the performance of the transformation process would be rather miserable \(a reality verified through testing!\) By allowing mutations in a tightly controlled and non-observable process, transformation can in fact be extremely fast, even with complex transformation pipelines.

### Composing Transforms

Transforms in auto-entity are intended to be composable. This allows discrete transforms of certain aspects of each entity to be encapsulated, often into generalized and reusable units, that can then be composed together as necessary for each entity that requires such transforms. 

#### Converting Data

Some of the most common applications for data transformation is to convert data from a server or wire representation into a more useful representation in the application. A key example of this is dates, which are most often transferred over the wire as ISO8601 formatted strings, as JavaScript `Date` objects cannot be properly serialized and deserialized in JSON. 

A date transform can make short work of keeping the data types and formats in line both for the app as well as for the wire:

```typescript
export const isoDateStringToDateObject = (prop: string) => {
    fromServer: data => ( // Gets a clone of data from the server
      data[prop] = data[prop] ? data[prop] = new Date(data[prop]) : data[prop], // mutate
      data // return mutated clone, will become entity stored in state
    ),
    toServer: entity => ( // Gets a clone of the entity from effect
      entity[prop] = entity[prop] ? entity[prop].toISOString() : entity[prop], // mutate
      entity // return mutated clone, will be wire-compatible data
    )
};
```

This simple unit of code is a highly reusable transform. Unlike the simpler example before, this transform is actually a function that takes a property name as a parameter, and returns a transform that can transform that property on an entity. This allows the transform to be applied to many entities with different date properties, or even many date properties on a single entity:

{% tabs %}
{% tab title="Order" %}
```typescript
@Entity({
  transform: [
    isoDateStringToDateObject('datePlaced'),
    isoDateStringToDateObject('dateProcessed'),
    isoDateStringToDateObject('dateShipped'),
    isoDateStringToDateObject('dateCanceled')
  ]
})
export class Order {
  @Key id: number;
  purchaseOrderNumber?: string;
  datePlaced: Date;
  dateProcessed?: Date;
  dateShipped?: Date;
  dateCanceled?: Date;
}
```
{% endtab %}

{% tab title="Customer" %}
```typescript
@Entity({
  transform: [
    isoDateStringToDateObject('dateAcquired')
  ]
})
export class Order {
  @Key id: number;
  name: string;
  dateAcquired: Date;
}
```
{% endtab %}
{% endtabs %}

Data conversions are likely to be the most common use case for using transforms, however they are not the only use case. 

#### Distributing and Merging Data

Another potential use case for transforms could be the distribution of information from one particular property, into several others that only exist for UI purposes, then the aggregation of those properties back into a single property for transfer over the wire back to the server. 

Sometimes rich information may be encoded in a single string. Such a string may represent numbers and names and other things. These aspects of the entity can be distributed out of the single string, into distinct properties of the entity making them easier to access and strongly typed. 

```typescript
export const distributePersonDetails = {
  fromServer: data => (_split: string[]) => (
    // split details encoded string on underscores
    split = data?.details?.split('_'),
    // distribute 
    data.name = split?.[0],
    data.age = Nunber(split?.[1]),
    data.sex = split?.[2],
    // remove unneeded details property
    data.details = undefined,
    // return mutated clone
    data
  )(),
  toServer: entity => (
    // aggregate details back into single properly formatted string
    entity.details = `${entity.name}_${entity.age}_${entity.sex}`,
    // remove extra properties so they don't serialize across wire
    entity.name = entity.age = entity.sex = undefined, 
    // return mutated clone
    entity
  )
};

export enum Sex {
  Male = 'M',
  Female = 'F'
}

@Entity('Person', {
  transform: [ distributePersonDetails ]
})
export class Person {
  @Key id: number;
  name: string;
  age: number;
  sex: Sex;
}
```

#### Generating Data

Finally, transforms may be used to generate data. Perhaps from other data on the entity, perhaps from data produced by another transform, which in effect can chain transforms. In the event that transforms are chained together, the order in which they are specified in the `transform` property of the configuration passed to `@Entity` becomes important.

```typescript
export const calculateYearOfBirth = {
  fromServer: entity => ( // chained transform, should get previously transformed ENTITY!
    entity.yearOfBirth = entity.age ? (new Date()).getFullYear() - entity.age : null,
    entity
  ),
  toServer: entity => (
    entity.yearOfBirth = undefined,
    entity
  )
};

export enum Sex {
  Male = 'M',
  Female = 'F'
}

@Entity('Person', {
  transform: [ distributePersonDetails, calculateYearOfBirth ]
})
export class Person {
  @Key id: number;
  name: string;
  age: number;
  sex: Sex;
  yearOfBirth: number;
}
```

