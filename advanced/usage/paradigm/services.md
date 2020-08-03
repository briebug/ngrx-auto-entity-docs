---
description: Understand changes to service implementation
---

# Services

With a normal @ngrx application, you are free to implement services however you see fit. There are no explicit requirements by @ngrx, since you are also on the hook to implement the effects as well, and it is your own effects that call your services. 

### Dynamic Services

NgRX Auto-Entities dynamic nature requires that data services implement the `IAutoEntityService<TModel>` interface. This interface defines the contract by which Auto-Entity interacts with your services. This interface is described as follows:

```typescript
export interface IAutoEntityService<TModel> {
  load?(entityInfo: IEntityInfo, keys: any, criteria?: any): Observable<TModel>;
  loadAll?(entityInfo: IEntityInfo, criteria?: any): Observable<TModel[]>;
  loadMany?(entityInfo: IEntityInfo, criteria?: any): Observable<TModel[]>;
  loadPage?(entityInfo: IEntityInfo, page: Page, criteria?: any): Observable<IEntityWithPageInfo<TModel>>;
  loadRange?(entityInfo: IEntityInfo, range: Range, criteria?: any): Observable<IEntityWithRangeInfo<TModel>>;

  create?(entityInfo: IEntityInfo, entity: TModel, criteria?: any, originalEntity?: TModel): Observable<TModel>;
  createMany?(entityInfo: IEntityInfo, entities: TModel[], criteria?: any, originalEntities?: TModel[]): Observable<TModel[]>;

  update?(entityInfo: IEntityInfo, entity: TModel, criteria?: any, originalEntity?: TModel): Observable<TModel>;
  updateMany?(entityInfo: IEntityInfo, entities: TModel[], criteria?: any, originalEntities?: TModel[]): Observable<TModel[]>;

  upsert?(entityInfo: IEntityInfo, entity: TModel, criteria?: any, originalEntity?: TModel): Observable<TModel>;
  upsertMany?(entityInfo: IEntityInfo, entities: TModel[], criteria?: any, originalEntities?: TModel[]): Observable<TModel[]>;
  
  replace?(entityInfo: IEntityInfo, entity: TModel, criteria?: any, originalEntity?: TModel): Observable<TModel>;
  replaceMany?(entityInfo: IEntityInfo, entities: TModel[], criteria?: any, originalEntities?: TModel[]): Observable<TModel[]>;

  delete?(entityInfo: IEntityInfo, entity: TModel, criteria?: any, originalEntity?: TModel): Observable<TModel>;
  deleteMany?(entityInfo: IEntityInfo, entities: TModel[], criteria?: any, originalEntities?: TModel[]): Observable<TModel[]>;
  
  deleteByKey?(entityInfo: IEntityInfo, key: EntityIdentity, criteria?: any): Observable<EntityIdentity>;
  deleteManyByKeys?(entityInfo: IEntityInfo, keys: EntityIdentity[], criteria?: any): Observable<EntityIdentity[]>;
}
```

This interface defines a different method that corresponds to each different kind of initiating generic action provided as a part of the NgRX Auto-Entity library. Every method provides `entityInfo` as the first parameter, which contains metadata about the entity, including its name \(based on the class name you defined for the model\) as well as its type \(the class you defined for the model\). Each method also accepts a variety of other parameters, whatever may be necessary to support the unique behavior of that method.

{% hint style="success" %}
Note that each method of the `IAutoEntityService` interface is **optional**. Implement only what you need!
{% endhint %}

### Bulk Operations

In addition to the basic CUD operations, we also support bulk versions of each CUD operation, as well as several options for loading entities. Each of the different load operations support different behavioral semantics, defined by the initiating actions and guiding how the meta reducer handles the data in state.

{% hint style="info" %}
Each method of the `IAutoEntityService` interface accepts, as the last parameter, custom `criteria`. This allows you, the developer, to include any additional, arbitrary details that may be necessary to facilitate the desired operation, for any operation supported by this library.
{% endhint %}

