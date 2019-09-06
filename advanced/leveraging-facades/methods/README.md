# The Interface: Methods

Prefabricated facades in NgRx Auto-Entity expose all the necessary properties and methods to allow you to fully leverage all of the state, actions and selectors Auto-Entity manages. Once you have extended a class from the base facade class, you may leverage all of this functionality without any additional effort.

### Action Dispatching Methods

In addition to properties for each selection, entity facades also expose a number of methods that wrap NgRx `action` dispatch calls. Each of these methods handles creation and subsequent dispatch of the appropriate generic action, including handling all the type information. The available methods on every facade include:

{% hint style="success" %}
**select\(entity: TModel\):** Selects a single entity   
**selectByKey\(key: EntityIdentity\):** Selects a single entity by the specified key  
**selectMany\(entities: TModel\[\]\):** Selects the specified entities  
**selectManyByKeys\(keys: EntityIdentity\[\]\):** Selects entities by the specified keys  
  
**deselect\(\):** Deselects any previously selected entity  
**deselectMany\(entities: TModel\[\]\):** Deselects the specified entities  
**deselectManyByKeys\(keys: EntityIdentity\[\]\):** Deselects the specified entities by keys  
**deselectAll\(\):** Deselects all selected entities

**load\(keys: any, criteria?: any\):** Loads entity by the specified key  
**loadMany\(criteria: any\):** Loads many entities by the specified criteria  
**loadAll\(criteria?: any\):** Loads all entities  
**loadPage\(page: Page, criteria?: any\):** Loads a page of entities  
**loadRange\(range: Range, criteria?: any\):** Loads a range of entities  
  
**create\(entity: TModel, criteria?: any\):** Creates a new entity  
**createMany\(entities: TModel\[\], criteria?: any\):** Bulk creates entities  
  
**update\(entity: TModel, criteria?: any\):** Updates an entity  
**updateMany\(entities: TModel\[\], criteria?: any\):** Bulk updates entities  
  
**replace\(entity: TModel, criteria?: any\):** Replace an entity  
**replaceMany\(entities: TModel\[\], criteria?: any\):** Bulk replace entities  
  
**delete\(entity: TModel, criteria?: any\):** Delete an entity  
**deleteMany\(entities: TModel\[\], criteria?: any\):** Bulk delete entities  
  
**clear\(\):** Clears all state for the entity type and resets it to empty 
{% endhint %}

### Familiar Functionality

All of these methods, as with the properties, are encapsulating functionality you may already be familiar with. In the past with plain old NgRx, you might have done something like the following:

```typescript
this.store.dispatch(new LoadAll(Customer, { organizationId: orgId }));
```

This is in fact what all of the entity facade methods are doing for you. We simplify the above repetitive procedure so you may simply call a method with just the bare bones required arguments, rather than have to bring in the store, dispatch and construct new actions yourself:

```typescript
loadAll(criteria?: any): void {
  this.store.dispatch(new LoadAll(this.modelType, criteria));
}
```

The model type is tracked when you extend the facade base class with your own concrete implementation:

```typescript
export class CustomerFacade extends CustomerFacadeBase {
    constructor(private store: Store<AppState>) {
        super(Customer, store); // Passing Customer here sets this.modelType
    }
}
```

