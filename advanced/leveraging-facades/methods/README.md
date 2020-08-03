# The Interface: Activities

Prefabricated facades in NgRx Auto-Entity expose all the necessary properties and methods to allow you to fully leverage all of the state, actions and selectors Auto-Entity manages. Once you have extended a class from the base facade class, you may leverage all of this functionality without any additional effort.

### Activities \(Action Dispatching Methods\)

In addition to properties for each selection, entity facades also expose a number of methods that wrap NgRx `action` dispatch calls. Each of these methods handles creation and subsequent dispatch of the appropriate generic action, including handling all the type information. We call these **activities.** The available methods on every facade include:

{% hint style="success" %}
**select\(entity: TModel\):** Selects a single entity   
**selectByKey\(key: EntityIdentity\):** Selects a single entity by the specified key  
**selectMany\(entities: TModel\[\]\):** Selects the specified entities  
**selectMore\(entities: TModel\[\]\):** Selects more entities and updates the current selection  
**selectManyByKeys\(keys: EntityIdentity\[\]\):** Selects entities by the specified keys  
**selectMoreByKeys\(keys: EntityIdentity\[\]\):** Selects more entities by the specified keys  
  
**deselect\(\):** Deselects any previously selected entity  
**deselectMany\(entities: TModel\[\]\):** Deselects the specified entities  
**deselectManyByKeys\(keys: EntityIdentity\[\]\):** Deselects the specified entities by keys  
**deselectAll\(\):** Deselects all selected entities

**load\(keys: any, criteria?: any, corrId?: string\):** Loads entity by the specified key  
**loadMany\(criteria: any, corrId?: string\):** Loads many entities by the specified criteria  
**loadAll\(criteria?: any, corrId?: string\):** Loads all entities  
**loadPage\(page: Page, criteria?: any, corrId?: string\):** Loads a page of entities  
**loadRange\(range: Range, criteria?: any, corrId?: string\):** Loads a range of entities  
  
**create\(entity: TModel, criteria?: any, corrId?: string\):** Creates a new entity  
**createMany\(entities: TModel\[\], criteria?: any, corrId?: string\):** Bulk creates entities  
  
**update\(entity: TModel, criteria?: any, corrId?: string\):** Updates an entity  
**updateMany\(entities: TModel\[\], criteria?: any, corrId?: string\):** Bulk updates entities

**upsert\(entity: TModel, criteria?: any, corrId?: string\):** Updates/inserts an entity  
**upsertMany\(entities: TModel\[\], criteria?: any, corrId?: string\):** Bulk upserts entities  
  
**replace\(entity: TModel, criteria?: any, corrId?: string\):** Replace an entity  
**replaceMany\(entities: TModel\[\], criteria?: any, corrId?: string\):** Bulk replace entities  
  
**delete\(entity: TModel, criteria?: any, corrId?: string\):** Delete an entity  
**deleteMany\(entities: TModel\[\], criteria?: any, corrId?: string\):** Bulk delete entities  
**deleteByKey\(key: EntityIdentity, criteria?: any, corrId?: string\)**: Delete an entity by its key  
**deleteManyByKeys\(keys: EntityIdentity\[\], criteria?: any, corrId?: string\)**: Delete many entities by their keys  
  
**clear\(\):** Clears all state for the entity type and resets it to empty 
{% endhint %}



{% hint style="info" %}
The last parameter of each activity is a correlation id. In the above descriptions, this has been shortened to the term `corrId` to condense this documentation a bit, however the actual parameter is called `correlationId` and is optional. This allows the end developer to control what the correlationId is for any given set of initiation/result actions dispatched with auto-entity. If this parameter is not provided, the correlationId will be a randomly generated uuid. 
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

