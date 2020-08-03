# Loading Ranges

In addition to our "standard" loading actions for single, all and many entity retrievals, we also provide two additional loading actions: `LoadPage` and `LoadRange`. You may wonder why both, and again it boils down to semantics, behavior. Both are necessary to support different kinds of data loading behavior, as will become evident here.

### Loading Ranges

Loading a "range" of data is similar in concept to loading a page of data, however it differs in semantics and actual behavior in the context of NgRx Auto-Entity and the way reduction of page loads are handled vs. the way reduction of ranged loads are handled. 

The `LoadRange` generic action supports a more modern approach to handling very large, and more specifically arbitrarily sized datasets called "infinite scrolling." This technique is usually implemented on lists of items, such as products in a catalog, that may have relatively arbitrary and often-changing total entity counts. When a user scrolls to the bottom of the browser page, the next "range" of products, starting from the last+1 of the currently displayed range, is automatically retrieved from the server and displayed as a continuation of any previously displayed list. 

#### Augmentation of Existing State

When loading a range, unlike loading a page, any newly loaded entities for this type will be **concatenated** into existing state, preserving any previously loaded entities and including any newly loaded entities. As with paged loads, this ensures that bandwidth is used efficiently, however keep in mind that for very large data sets, continuous loading of subsequent ranges could eventually require significant amounts of browser memory to store all the information in state. Further, due to the `pure` \(side-effect free\) nature by which new versions of state must be created with @ngrx, updating state with a large volume of previously loaded entities may start to become a lengthy operation.

### Ranged Load Implementation

Loading ranges of data one at a time with NgRx Auto-Entity is also very easy. Simply use the `LoadRange` action, or the `loadRange` method on your entity facades, along with the `Range` parameter:

```typescript
this.customerFacade.loadRange({ first: 26, last: 50 });
```

When implementing your entity service, you will have access to the `Range` object provided in the initial action, which may be leveraged in your API call:

```typescript
loadRange(entityInfo: IEntityInfo, {first, last}: Range, criteria?: any)
    : Observable<IEntityWithRangeInfo<Customer>> {
        return this.http.get<Customer[]>(
            `${environment.apiBaseUrl}/api/v1/customers` { 
                params: {first, last}
            } 
        ).pipe(
            map(customers => ({
                rangeInfo: {
                    range: {first, last},
                    totalCount: Infinity
                },
                entities: customers
            }))
        );
    }
```

Note the use of `pipe()` on the `http` call here, and the transformation of the response. Also not the return type of the `loadRange` method.  NgRx Auto-Entity _**requires**_ additional meta-data about what range was loaded as well as the total number of entities that may be ranged through, in order to store that information in state and make it available to components and your UI \(for proper handling of infinite scrolling behavior, for example\).

### Entities with Range Info

It is important to understand the nature of the data that must be returned by the `loadRange` method in your entity services. The return type is `IEntityWithRangeInfo<TModel>` which is defined as so:

```typescript
export interface IEntityWithRangeInfo<TModel> {
    entities: TModel[];
    rangeInfo: IRangeInfo;
}
```

The child type of the property `rangeInfo`, of type `IRangeInfo` is defined as so:

```typescript
export declare type RangeValue = string | number | Date;

export interface IStartEndRange {
    start: RangeValue;
    end: RangeValue;
}
export interface IFirstLastRange {
    first: RangeValue;
    last: RangeValue;
}
export interface ISkipTakeRange {
    skip: number;
    take: number;
}
export declare type Range = IStartEndRange | IFirstLastRange | ISkipTakeRange;

export interface IRangeInfo {
    range: Range;
    totalCount: number;
}
```

### Total Counts

Unlike with paged data, where a total count is essential in order to properly calculate how many pages of data there are, ranged data does not necessarily require an exact total count. For use cases where a total count is not required, you may simply specify `Infinity` or any other constant that allows you to implement your ranged loads and UI behavior properly. 

In the event that you do require an actual total count, see the [paged loads documentation](pages.md) for various ways to retrieve total counts from the server.

