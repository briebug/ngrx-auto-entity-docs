# Loading Pages

In addition to our "standard" loading actions for single, all and many entity retrievals, we also provide two additional loading actions: `LoadPage` and `LoadRange`. You may wonder why both, and again it boils down to semantics, behavior. Both are necessary to support different kinds of data loading behavior, as will become evident here.

### Paged Data

Loading of paged data is a common practice that dates back many years, in fact decades. It is a fundamental kind of data load, and extensive research has been done as well as dedicated features to support paged data have been integrated into data storage solutions. 

Loading pages of data, rather than loading complete sets of data, is often essential. This is especially true in the modern world where we frequently must acquire and work with immense datasets that can number in the millions, billions...even trillions of records. With such numbers, it should be obvious that loading all entities for such datasets would be problematic at best, assuming it was even possible in the first place. Large datasets will usually consume immense space, including immense bandwidth and immense amounts of memory in a browser. 

#### Replacement of Existing State

When loading a page, any previously loaded entities for this type will be **replaced** in by the new page of entities retrieved. This ensures that bandwidth and browser memory space is used efficiently, and that memory is not wasted trying to store an ever-growing number of entities as a user cycles through pages in your UI. 

### Paged Load Implementation

Loading pages of data one at a time with NgRx Auto-Entity is quite easy. Simply use the `LoadPage` action, or the `loadPage` method on your entity facades, along with the `Page` parameter:

```typescript
this.customerFacade.loadPage({ page: 2, size: 25 });
this.customerFacade.loadPage({ page: 2 }); // Size may be optional
```

When implementing your entity service, you will have access to the `Page` object provided in the initial action, which may be leveraged in your API call:

```typescript
loadPage(entityInfo: IEntityInfo, {page=1, size=25}: Page, criteria?: any)
    : Observable<IEntityWithPageInfo<Customer>> {
        const skip = (page-1) * size;
        const take = size;
        
        return this.http.get<Customer[]>(
            `${environment.apiBaseUrl}/api/v1/customers?skip=${skip}&take=${take}`
        ).pipe(
            map(customersMeta => ({
                pageInfo: {
                    page: {
                        page: customersMeta.pageNo,
                        size
                    },
                    totalCount: customersMeta.totalRecordCount
                },
                entities: customersMeta.customers
            }))
        );
    }
```

Note the use of `pipe()` on the `http` call here, and the transformation of the response. Also not the return type of the `loadPage` method.  NgRx Auto-Entity requires additional meta-data about which page was loaded as well as the total number of entities that may be paged through, in order to store that information in state and make it available to components and your UI \(for proper rendering of paging controls, for example\).

### Entities with Page Info

It is important to understand the nature of the data that must be returned by the `loadPage` method in your entity services. The return type is `IEntityWithPageInfo<TModel>` which is defined as so:

```typescript
export interface IEntityWithPageInfo<TModel> {
    entities: TModel[];
    pageInfo: IPageInfo;
}
```

The child type of the property `pageInfo`, of type `IPageInfo` is defined as so:

```typescript
export interface IPage {
    page: number;
    size: number;
}
export declare type Page = IPage;

export interface IPageInfo {
    page: Page;
    totalCount: number;
}
```

### Separate Total Count Call

One way or another, you must be able to provide the  above information for each paged load. You may simply prefer to reflect back the original page information sent in for the `IPageInfo` page property, however usually the `totalCount` must be returned by the server in one way or another. It need not necessarily be on the same call, an additional call may be performed to retrieve the total count of entities:

```typescript
loadPage(entityInfo: IEntityInfo, {page=1, size=25}: Page, criteria?: any)
    : Observable<IEntityWithPageInfo<Customer>> {
        const skip = (page-1) * size;
        const take = size;
        
        return this.http.get<Customer[]>(
            `${environment.apiBaseUrl}/api/v1/customers?skip=${skip}&take=${take}`
        ).pipe(
            withLatestFrom(
                this.http.get<{totalRecords: number}>(
                    `${environment.apiBaseUrl}/api/v1/customers?totalOnly=true`
                );
            )
            map(([customers, total]) => ({
                pageInfo: {
                    page: { page, size },
                    totalCount: total.totalRecords
                },
                entities: customers
            }))
        );
    }
```

### Content-Range HTTP Header

Another possibility, although potentially not canonical standard depending on exactly how you implement it, might be to use the HTTP `Content-Range` header in your responses to support additional metadata about page info, without having to include it in the body of the response. This header is normally used with a unit of bytes, however technically speaking the units can be arbitrary \(such as, say, `records` or `entities`\):

```typescript
loadPage(entityInfo: IEntityInfo, {page=1, size=25}: Page, criteria?: any)
    : Observable<IEntityWithPageInfo<Customer>> {
        const skip = (page-1) * size;
        const take = size;
        
        return this.http.get<HttpResponse<Customer[]>>(
            `${environment.apiBaseUrl}/api/v1/customers?skip=${skip}&take=${take}`
        ).pipe(
            map(response => ({
                customers: response.body,
                contentRange: response.headers.has('Content-Range') ? 
                    // <units> <start>-<end>/<size>
                    // entities 0-25/102942
                    // records 25-50/102942
                    response.headers.get('Content-Range').split('/')[1] : 0
            }),
            map(({customers, total}) => ({
                pageInfo: {
                    page: { page, size },
                    totalCount: +total
                },
                entities: customers
            }))
        );
    }
```

#### 

