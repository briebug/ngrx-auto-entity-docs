# The IEntityState Interface

Core to NgRx Auto-Entity is it's internal state structure for each entity. You may have noticed the little `IEntityState<TModel>` interface floating around prior documentation, including the [Quick Start](../../../getting-started/from-scratch/). This interface is much like the `EntityState<T>` interface from @ngrx/entity, although more complex as Auto-Entity manages more state for you.

```typescript
export interface IEntityDictionary<TModel> {
  [key: string]: TModel;
}

export type EntityIdentity = string | number;

export interface IEntityState<TModel> {
  entities: IEntityDictionary<TModel>;
  ids: EntityIdentity[];
  currentEntityKey?: EntityIdentity;
  currentEntitiesKeys?: EntityIdentity[];
  currentPage?: Page;
  currentRange?: Range;
  totalPageableCount?: number;
  isLoading?: boolean;
  isSaving?: boolean;
  isDeleting?: boolean;
  loadedAt?: Date;
  savedAt?: Date;
  deletedAt?: Date;
}
```

While our internal types are named slightly differently, the structure of our `IEntityState` interface is name-compatible with the structure @ngrx/entity expects. As such, with perhaps a little type coercion when necessary, it should be possible to utilize @ngrx/entity functionality such as its adapter to update Auto-Entity state...if the need ever arose.

### Additional State

You may notice that we track a lot of additional but optional state as well. This includes the current entity, information about the current page or range loaded into state, as well as various flags and timestamps. 

{% hint style="info" %}
This interface is fundamental to all entities who's state is managed by NgRx Auto-Entity. You can and should use this interface wherever a state interface is required, such as root or feature state interfaces that will be used in a reducer map. 
{% endhint %}

Learn this interface if you intend to leverage any of the lower level capabilities of NgRx Auto-Entity. In particular, if you ever provide extra initial state, make sure you know what properties are involved in the event you wish to ADD new state information, or SET existing state information with initial values. 

