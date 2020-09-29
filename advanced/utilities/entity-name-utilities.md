---
description: Who goes there?
---

# Entity Name Utilities

Every automatic entity can be decorated with up to three distinct names. The `modelName` which is the _standard_, unique name of the entity, even in minified, uglified, optimized code. The `pluralName` which is the name of the entity that may be used in messaging to the end user. The `uriName` which is the name of the entity as it should be used in urls to RESTful APIs. 

All of these functions may be passed a model type, or an entity instance, so long as the instance is properly prototyped by the model type. Utilize the previously described `makeEntity` function to convert POJOs into prototyped entities. 

### Standard Name

You may retrieve any of these names using the entity name utility functions. The standard name of any entity, the name used in state, and the core name used by auto-entity to identify your model, state, etc. may be retrieved with the `nameOfEntity()` function. 

```typescript
@Entity({modelName: 'Customer'})
export class Customer {
}

// elsewhere:
nameOfEntity(Customer); -> 'Customer'
nameOfEntity(makeCustomer(customer)); -> 'Customer'
```

### Plural Name

Auto-Entity allows you to decorate your entities with a plural name as well. Internally, auto-entity does not use this directly. It is intended to allow easy retrieval of a properly pluralized name in the end developer's code. Notably, this may be the case in a dynamic entity service created by the end developer, when logging messages about the entity, etc. You may retrieve this name with the `pluralNameOfEntity()` function. 

```typescript
@Entity({
    modelName: 'Customer',
    pluralName: 'customers'
})
export class Customer {
}

// elsewhere:
pluralNameOfEntity(Customer); -> 'customers'
pluralNameOfEntity(makeCustomer(customer)); -> 'customers'
```

#### Example 1:

A common use case for `pluralName` is when generating dynamic messages about entity actions. For example, an effect that toasts an error whenever a Failure action is dispatched might use the plural name for any of the "All", "Many", "Page" or "Range" actions:

```typescript
const ENTITY_FAILURES = [
    EntityActionTypes.LoadFailure,
    EntityActionTypes.LoadAllFailure,
    EntityActionTypes.LoadManyFailure,
    EntityActionTypes.LoadPageFailure,
    EntityActionTypes.LoadRangeFailure,
    EntityActionTypes.CreateFailure,
    EntityActionTypes.CreateManyFailure,
    EntityActionTypes.UpdateFailure,
    EntityActionTypes.UpdateManyFailure,
    EntityActionTypes.UpsertFailure,
    EntityActionTypes.UpsertManyFailure,
    EntityActionTypes.ReplaceFailure,
    EntityActionTypes.ReplaceManyFailure,
    EntityActionTypes.DeleteFailure,
    EntityActionTypes.DeleteManyFailure,
];

const PLURALS = ['Many', 'All', 'Page', 'Range'];

toastEntityFailure$ = createEffect(
    () => this.actions$.pipe(
        ofEntityAction(ENTITY_FAILURES),
        throttleTime(10000),
        map({type, info: { modelType }) => ({ 
            type, 
            modelType, 
            terms: type.split(' ') 
        })),
        map(({type, modelType, [, , action]) => ({
            modelType,
            isPlural: PLURALS.some(term => type.includes(term)),
            action
        })),
        mergeMap(({ modelType, isPlural, action}) => 
            this.toastService.error(`Error ${action}ing ${isPlural 
                ? pluralNameOfEntity(modelType)
                : nameOfEntity(modelType)
            }.`)
        )
    ),
    { dispatch: false }
);
```

### URI Name

Another name Auto-Entity supports is the URI name. This name is explicitly provided in the event that the name of your entity, as used in urls to API endpoints. Even if an entity name in a url is plural, they are often formatted differently...such as kebab case. You may retrieve this name with the `uriNameOfEntity()` function.

```typescript
@Entity({
    modelName: 'LineItem',
    pluralName: 'LineItems',
    uriName: 'line-items'
})
export class LineItem {
}

// elsewhere:
uriNameOfEntity(LineItem); -> 'line-item'
uriNameOfEntity(makeLineItem(lineItem)); -> 'line-item'
```

#### Example 1:

A common use case is when implementing auto entity services. The `uriName` allows urls to be dynamically built from the entity metadata, allowing the developer to share entity services across entities:

```typescript
export class EntityService implements IAutoEntityService {
  load(info: IEntityInfo, key: number): Observable<any> {
    return this.http.get<any>(`${environment.apiRoot}/api/${uriNameOfEntity(info.modelType)}`
  }
}
```

Note that it is also possible to use just `info.uriName` as well, as the entity options are supplied along with the `modelType` on `IEntityInfo`. The use of `uriNameOfEntity()` is an alternative method of getting the name.

#### Example 2:

Another use case is when implementing a dynamic entity service that supports hierarchical API endpoints. An example may be getting `LineItem`s for `Order`s. You may need to build a structured url along the lines of `/orders/:order-id/line-items/:line-item-id` in order to make the proper request. The use of custom criteria can assist here:

```typescript
export interface EntityCriteria {
    parents?: { [key: string]: string|number }
}
```

Custom criteria may be passed along with entity actions, say from an effect:

```typescript
loadLineItems$ = createEffect(
    () => this.actions$.pipe(
        ofEntityType(Order, EntityActionTypes.LoadSuccess),
        map(({entity: order}) => new LoadAll(LineItem, {
            parents: {
                [uriNameOfEntity(Order)]: getEntityKeyFromModel(Order, order)
            }
        })
    )
);
```

### State Name

The final entity name supported by Auto-Entity is the state name. The state name is the name that Auto-Entity expects to be used when you include your entity state within either app state or feature state, and is derived from the `modelName`. Auto-entity uses this name internally when accessing state and when messaging about the state property. You may retrieve this name with the `stateNameOfEntity()` function.

```typescript
@Entity({
    modelName: 'LineItem'
})
export class LineItem {
}

// elsewhere:
stateNameOfEntity(LineItem); -> 'lineItem'
stateNameOfEntity(makeLineItem(lineItem)); -> 'lineItem'
```

