# The Interface: Selections

Prefabricated facades in NgRx Auto-Entity expose all the necessary properties and methods to allow you to fully leverage all of the state, actions and selectors Auto-Entity manages. Once you have extended a class from the base facade class, you may leverage all of this functionality without any additional effort.

### Selections \(Observable Data Properties\)

Each entity facade exposes a number of properties that wrap NgRx state `selections`, exposing the streaming data within. Each of these properties returns and Observable of the appropriate type based on the state Auto-Entity tracks. We call these **selections.** The available properties on every entity facade include the following:

{% hint style="success" %}
**all$**: Observable&lt;TModel\[\]&gt;; Array of entity objects in state  
**entities$**: Observable&lt;IEntityDictionary&lt;TModel&gt;&gt;; Map of ids to entity objects  
**ids$**: Observable&lt;EntityIdentity\[\]&gt;; Array of entity identities in state   
**total$**: Observable&lt;number&gt;; Total umber of entities in state

**current$**: Observable&lt;TModel&gt;; Selected single entity  
**currentKey$**: Observable&lt;EntityIdentity&gt;; Key of selected single entity

**currentSet$**: Observable&lt;TModel\[\]&gt;; Selected entities _\(New v0.2\)_  
**currentSetKeys$**: Observable&lt;EntityIdentity\[\]&gt;; Keys of selected entities _\(New v0.2\)_

**currentPage$**: Observable&lt;Page&gt;; Info about the current page of entities in state  
**currentRange$**: Observable&lt;Range&gt;; Info about the current range of entities in state  
**totalPageable$**: Observable&lt;number&gt;; Total number of entities that can be paged

**edited$**: Observable&lt;Partial&lt;TModel&gt;&gt;; The currently edited entity \(partial\)  
**isDirty$**: Observable&lt;boolean&gt;; Flag indicating if there have been changes to edited entity

**isLoading$**: Observable&lt;boolean&gt;; Flag indicating if entities are currently loading  
**isSaving$**: Observable&lt;boolean&gt;; Flag indicating if entities are currently saving  
**isDeleting$**: Observable&lt;boolean&gt;; Flag indicating if entities are currently deleting

**loadedAt$**: Observable&lt;Date&gt;; Timestamp of last load  
**savedAt$**: Observable&lt;Date&gt;; Timestamp of last save  
**createdSt$**: Observable&lt;Date&gt;; Timestamp of last creation  
**deletedAt$**: Observable&lt;Date&gt;; Timestamp of last delete
{% endhint %}

### Familiar Functionality

All of these properties are encapsulating functionality you may already be familiar with. In the past with plain old NgRx, you might have done something like the following:

```typescript
this.customers$ = this.store.select(allCustomers);
```

This is in fact what all of the entity facade properties are doing for you. We simplify the above repetitive procedure so you may simply access a property rather than have to bring in the store, pipe and select yourself:

```typescript
get all$(): Observable<TModel> {
    return this.store.select(selectAll);
}
```

### Still Observable

Since all of our entity facade properties are observables, you are still free to use `.pipe()` on them as necessary, and leverage the full power of RxJs and reactive programming.

```typescript
this.customers$ = this.customerFacade.all$.pipe(
    withLatestFrom(this.activatedRoute.paramMap),
    map(([customers, params]) => [customers, params.get('search')]),
    map(([customers, search]) => 
        customers.map(customer => customer.name.match(search))
    )
);
```

