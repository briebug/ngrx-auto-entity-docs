# Entity Comparers

Auto-Entity provides some rich sorting capabilities. Every entity may define a default comparer as well as one or more named comparers. Auto-entity uses these utility functions internally whenever you use one of the sort selectors, however one of these functions is also exposed in the public API in the event you need to perform your own sorts of your own entities. This may occur often enough in custom selectors, where entities sorted in specific orders may be necessary for selectors to function properly.

### entityComparer

The utility function for getting comparers for an entity is `entityComparer()` which encapsulates all of the comparer retrieval functionality. Using this function, you may retrieve the default comparer or any named comparer, for an entity specified by its model type or a prototyped entity object. You may then use that comparer with `Array.sort`.

Normally, you will want to pass the entity model along with the name of the comparer. If you wish to retrieve the default comparer, the name is optional.

```typescript
@Entity({
  modelName: 'Customer',
  comparer: compareName,
  comparers: {
    byId: compareId
  }
})
export class Customer {
  @Key id: number;
  name: string;
}

const customerComparer = entityComparer(Customer);
const customerComparer2 = entityComparer(Customer, 'default');
const customerByIdComparer = entityComparer(Customer, 'byId');
```



