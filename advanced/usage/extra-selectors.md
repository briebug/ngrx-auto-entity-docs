---
description: Going the extra mile
---

# Extra Selectors

In addition to the "common" selectors that match @ngrx/entity selectors from the adaptor, NgRx Auto-Entity also provides selectors for accessing all of the extra state it also automatically tracks and manages for you. This includes loading/saving/deleting timestamps as well as flags, currently selected entities, etc.

{% hint style="success" %}
* selectCurrentEntity
* selectCurrentEntities
* selectCurrentEntityKey
* selectCurrentEntitiesKeys
* selectEditedEntity
* selectIsDirty
* selectCurrentPage
* selectCurrentRange
* selectTotalPageable
* selectIsLoading
* selectIsSaving
* selectIsDeleting
* selectLoadedAt
* selectSavedAt
* selectCreatedAt
* selectDeletedAt
{% endhint %}

As with common selectors, these are returned from our `buildState` utility function as part of the `ISelectorMap<TParentState, TModel>`. See the documentation on `buildState` for the full interface definition. You export these selectors the same as any other, via destructuring the `selectors` object returned by `buildState`.

### Sparse State by Default

It should be noted that all state managed by Auto-Entity is "sparse" or lightly populated. This means that some state, say the current page or range, the total pageable count, even the current entity, may be null or undefined until such time as an action is dispatched that would result in that state becoming populated. As such, expect that depending on the actual state of the application, `null` or `undefined` may indeed be the result of using any of the above selectors. 

