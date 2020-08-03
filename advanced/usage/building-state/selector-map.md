# The Selector Map

Another lower level element returned by `buildState` is the **selector map.** Much like @ngrx/entity, the selector map is a simple object \(associative array\) that maps common names to selector functions. 

```typescript
export interface ISelectorMap<TParentState, TModel> {
  selectIds: MemoizedSelector<object | TParentState, EntityIdentity[]>;
  selectEntities: MemoizedSelector<object | TParentState, IEntityDictionary<TModel>>;
  selectAll: MemoizedSelector<object | TParentState, TModel[]>;
  selectAllSorted: MemoizedSelector<object | TParentState, TModel[]>;
  selectTotal: MemoizedSelector<object | TParentState, number>;
  selectCurrentEntity: MemoizedSelector<object | TParentState, TModel | null>;
  selectCurrentEntityKey: MemoizedSelector<object | TParentState, EntityIdentity | null>;
  selectCurrentEntities: MemoizedSelector<object | TParentState, TModel[]>;
  selectCurrentEntitiesKeys: MemoizedSelector<object | TParentState, EntityIdentity[]>;
  selectEditedEntity: MemoizedSelector<object | TParentState, Partial<TModel> | null>;
  selectIsDirty: MemoizedSelector<object | TParentState, boolean>;
  selectCurrentPage: MemoizedSelector<object | TParentState, Page | null>;
  selectCurrentRange: MemoizedSelector<object | TParentState, Range | null>;
  selectTotalPageable: MemoizedSelector<object | TParentState, number>;
  selectIsLoading: MemoizedSelector<object | TParentState, boolean>;
  selectIsSaving: MemoizedSelector<object | TParentState, boolean>;
  selectIsDeleting: MemoizedSelector<object | TParentState, boolean>;
  selectLoadedAt: MemoizedSelector<object | TParentState, Date | null>;
  selectSavedAt: MemoizedSelector<object | TParentState, Date | null>;
  selectCreatedAt: MemoizedSelector<object | TParentState, Date | null>;
  selectDeletedAt: MemoizedSelector<object | TParentState, Date | null>;
}
```

As with the `IEntityState` interface, we have attempted to maintain naming compatibility with @ngrx/entity here, however also as with `IEntityState` we provide a lot more than @ngrx/entity does as well. Selectors are available for every state property managed by NgRx Auto-Entity.

