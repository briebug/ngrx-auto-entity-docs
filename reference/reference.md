# Reference documentation
> Note: This section of the ngrx-auto-entity documentation is a work in progress. A lot of features are currently missing, incomplete, or out of date. Feel free to ask questions on github or by e-mail if needed!

<!-- Section: Functions -->
### **Functions**
| Name | Arguments | Return Type | Description |
| ---- | --------- | ----------- | ----------- |
| buildState | type: IModelClass\<TModel\> - *the entity class* <br> extraInitialState?: any - *the (optional) initial state* | IModelState\<TParentState, TState, TModel\> | Builds the initial NgRx state for an entity and provides a base Facade class that can be extended. |

<!-- -->
<br>
<br>
<!-- -->

<!-- Section: Decorators -->
### **Decorators**
| Name | Factory? | Arguments | Description |
| ---- | -------- | --------- | ----------- |
| Key | No | - | Designates the key property for the entity |

{% code-tabs %}
{% code-tabs-item title="Usage example" %}
```typescript
import { Key } from '@briebug/ngrx-auto-entity';

export class Account {
  @Key id: number;
  customer_id: number;
  accountNumber: number;
  name: string;
  amount: number;
}
```

<!-- -->
<br>
<br>
<!-- -->

<!-- Section: Facade -->
### **Facade**
Prefabricated facades come complete with a core set of properties and methods that may be used "out of the box" without any additional work on your part beyond extending the base facade class.

<br>

#### Properties (Selectors)
Getters that will return an observable containing data on the **Facade** model's entities and state.
| Property | Return Type | Description |
| -------- | ----------- | ------- |
| all$ | Observable\<TModel[]\> | Get all entities as an array. |
| entities$ | Observable\<IEntityDictionary\<TModel\>\> | Get all entities as a dictionary. |
| ids$ | Observable\<EntityIdentity[]\> | Get all entity ids as an array. |
| total$ | Observable\<number\> | Get the number of entities. |
| current$ | Observable\<TModel\> | Get the currently selected entity. |
| currentKey$ | Observable\<EntityIdentity\> | Get the currently selected entity's id. |
| currentSet$ | Observable\<TModel[]\> | Get the currently selected entities as an array. |
| currentSetKeys$ | Observable\<EntityIdentity[]\> | Get the currently selected entities' ids as an array. |
| edited$ | Observable\<Partial\<TModel\>\> | TBD |
| isDirty$ | Observable\<boolean\> | TBD |
| currentPage$ | Observable\<Page\> | Get the current **Page** if using pagination. |
| currentRange$ | Observable\<Range\> | Get the current **Range** if using pagination. |
| totalPageable$ | Observable\<number\> | TBD |
| isLoading$ | Observable\<boolean\> | Get whether this **Facade** is loading. |
| isSaving$ | Observable\<boolean\> | Get whether this **Facade** is saving. |
| isDeleting$ | Observable\<boolean\> | Get whether this **Facade** is deleting. |
| loadedAt$ | Observable\<Date\> | Get the Date object representing when entities were loaded last. |
| savedAt$ | Observable\<Date\> | Get the Date object representing when entities were loaded last. |
| createdAt$ | Observable\<Date\> | Get the Date object representing when an entity was created last. |
| deletedAt$ | Observable\<Date\> | Get the Date object representing when an entity was deleted last. |

<br>

#### Methods (Actions)
Functions for manipulating the **Facade** model's entities and state (note: all of their return type is void).

| Name | Arguments | Description |
| ---- | --------- | ----------- |
| select | entity: TModel | TBD |
| selectByKey | key: EntityIdentity | TBD |
| selectMany | entities: TModel[] | TBD |
| selectMore | entities: TModel[] | TBD |
| selectManyByKeys | keys: EntityIdentity[] | TBD |
| selectMoreByKeys | keys: EntityIdentity[] | TBD |
| deselect | - | TBD |
| deselectMany | entities: TModel[] | TBD |
| deselectManyByKeys | keys: EntityIdentity[] | TBD |
| deselectAll | - | TBD |
| edit | entity: Partial<TModel> | TBD |
| change | entity: Partial<TModel> | TBD |
| endEdit | - | TBD |
| load | keys: any, *criteria?: any* | TBD |
| loadMany | criteria: any | TBD |
| loadAll | *criteria?: any* | TBD |
| loadPage | page: Page, *criteria?: any* | TBD |
| loadRange | range: Range, *criteria?: any* | TBD |
| create | entity: TModel, *criteria?: any* | TBD |
| createMany | entities: TModel[], *criteria?: any* | TBD |
| update | entity: TModel, *criteria?: any* | TBD |
| updateMany | entities: TModel[], *criteria?: any* | TBD |
| replace | entity: TModel, *criteria?: any* | TBD |
| replaceMany | entities: TModel[], *criteria?: any* | TBD |
| delete | entity: TModel, *criteria?: any* | TBD |
| deleteMany | entities: TModel[], *criteria?: any* | TBD |
| clear | - | TBD |