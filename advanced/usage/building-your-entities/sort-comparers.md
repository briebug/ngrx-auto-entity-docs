# Sort Comparers

Sorting is a core feature of most applications that use data. At one point or another, you will likely want to display your data sorted in one order or another. Auto-Entity provides built in functionality for sorting your entities. The most common use case would be to define a default comparer, however auto-entity also supports defining additional named comparers to sort your entities in any number of ways. 

### Default Comparer

To attach a default comparer to your entity, use the `comparer` property of the configuration object you pass to the `@Entity` decorator. This is a standard comparer function that you should be familiar with for standard JavaScript sort functionality.

```typescript
import { Entity } from '@briebug/ngrx-auto-entity'

 @Entity('Customer', { 
   comparer: (a, b) => a.name?.localeCompare(b.name)
 })
 export class Customer {
   @Key id: number;
   name: string;
 }
```

Sort comparers may be defined directly within the `@Entity` decorator, or may be defined as a constant or function and passed in the decorator. This allows a single comparer to be used for multiple entities, as appropriate:

```typescript
import { Entity } from '@briebug/ngrx-auto-entity'

export interface HasName {
  name: string;
}

export const compareName = (a: HasName, b: HasName) => a.name?.localeCompare(b.name)

@Entity('Customer', { 
  comparer: compareName
})
export class Customer {
  @Key id: number;
  name: string;
  // ...
}

@Entity('Supplier', { 
  comparer: compareName
})
export class Supplier {
  @Key id: number;
  name: string;
  // ...
}
```

### Named Comparers

If you find yourself in a situation where you need to sort entities in more than one way, then you can define named comparers. Named comparers are used with the  the CustomSorted selector, which is a parameterized selector. Being parameterized, each time you use it with a different parameter, its memoized result is recomputed. This ensures that you don't use too much memory when sorting the same set of entities in different ways, however at greater compute cost.

To define named comparers, use the `comparers` property of the configuration object you pass to the `@Entity` decorator. This property expects an object, or an associative map, where the properties are the names of the comparers, and the value of each property is the comparer function.

```typescript
import { Entity } from '@briebug/ngrx-auto-entity'

export interface HasNumericId {
  id: number;
}

export const compareId = (a: HasNumericId, b: HasNumericId) => 
  a.id - b.id;

export interface HasName {
  name: string;
}

export const compareName = (a: HasName, b: HasName) =>
  a.name?.localeCompare(b.name)

@Entity('Customer', { 
  comparer: compareName,
  comparers: {
    byId: compareId,
    byDateAcquiredDesc: (a, b) => b.dateAcquired - a.dateAcquired
  }
})
export class Customer {
  @Key id: number;
  name: string;
  dateAcquired: Date;
  // ...
}

@Entity('Supplier', { 
  comparer: compareName,
  comparers: {
    byId: compareId,
    byLastShipment: (a, b) => a.dateOfLastShipment - b.dateOfLastShipment
  }
})
export class Supplier {
  @Key id: number;
  name: string;
  dateOfLastShipment: Date;
  // ...
}
```

To get a named comparer for an entity, you can use the comparer [Utility Functions](../../utilities/entity-comparers.md). Further, you can use the `customSorted` selector and pass the name of the comparer you wish to use for the sort, which will sort with that comparer on selection. 

### Default Named Comparer

When defining named comparers, you may also define the default within the `comparers` property of the configuration object passed to `@Entity`. This is an alternative method of defining a default comparer.

```typescript
import { Entity } from '@briebug/ngrx-auto-entity'

export interface HasName {
  name: string;
}

export const compareName = (a: HasName, b: HasName) =>
  a.name?.localeCompare(b.name)

@Entity('Customer', { 
  comparers: {
    default: compareName,
  }
})
export class Customer {
  @Key id: number;
  name: string;
  // ...
}
```

Note that when defining default comparers, if you define both the `comparer` property as well as the `default` named comparer of the `comparers` property, the comparer defined on the `comparer` property will always take precedence. As such, this configuration will sort by name, not id:

```typescript
import { Entity } from '@briebug/ngrx-auto-entity';

export interface HasNumericId {
  id: number;
}

export const compareId = (a: HasNumericId, b: HasNumericId) => 
  a.id - b.id;

export interface HasName {
  name: string;
}

export const compareName = (a: HasName, b: HasName) =>
  a.name?.localeCompare(b.name)

@Entity('Customer', { 
  comparer: compareName,
  comparers: {
    default: compareId,
  }
})
export class Customer {
  @Key id: number;
  name: string;
  // ...
}
```

