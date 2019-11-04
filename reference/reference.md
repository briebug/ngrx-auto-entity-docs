# Reference documentation
> Note: This section of the ngrx-auto-entity documentation is a work in progress. A lot of features are currently missing, incomplete, or out of date. Feel free to ask questions on github or by e-mail if needed!

<!-- -->
<br>
<br>
<!-- -->

<!-- Section: Modules -->
## **Modules**

<!-- NgrxAutoEntityModule -->
`NgrxAutoEntityModule` - The main module that should be imported into the app module or feature modules.

**For the AppModule:**
{% code-tabs %}
{% code-tabs-item title="src/app/app.module.ts" %}
```typescript
import { NgrxAutoEntityModule } from '@briebug/ngrx-auto-entity';
// ... other imports ...

@NgModule({
    imports: [
        BrowserModule,
        StoreModule.forRoot(appReducer, { metaReducers: appMetaReducers }),
        EffectsModule.forRoot([]),
        NgrxAutoEntityModule.forRoot() // Add this!
    ]
})
export class AppModule {}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

**For a feature module:**
{% code-tabs %}
{% code-tabs-item title="src/app/+orders/orders.module.ts" %}
```typescript
import { NgrxAutoEntityModule } from '@briebug/ngrx-auto-entity';
// ... other imports ...

@NgModule({
    imports: [
        CommonModule,
        StoreModule.forFeature('orders', featureReducer),
        NgrxAutoEntityModule.forFeature() // Changed from forRoot() to forFeature()
    ]
})
export class AppModule {}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

<!-- -->
<br>
<br>
<!-- -->

<!-- Section: Functions -->
## **Functions**

<!-- buildState -->
`buildState` - Builds the initial NgRx state for an entity and provides a base Facade class that can be extended.

```typescript
buildState(type: IModelClass<TModel>, extraInitialState?: any): IModelState<TParentState, TState, TModel>
```

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| type | IModelClass\<TModel\> | the entity class |
| *extraInitialState?* | *any* | *the (optional) initial state* |

**Returns** [IModelState](#imodelstate)

<br>
<br>

<!-- buildFeatureState -->
`buildFeatureState` - Builds the Ngrx state for an entity that is part of a feature module.

```typescript
buildFeatureState<TState extends IEntityState<TModel>, TParentState, TModel>(
  type: IModelClass<TModel>,
  featureStateName: NonNullable<string>,
  selectParentState: MemoizedSelector<object, TParentState>,
  extraInitialState?: any
): IModelState<TParentState, TState, TModel>
```

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| type | IModelClass\<TModel\> | the entity class |
| featureStateName | NonNullable\<string\> | the name of the feature state |
| selectParentState | MemoizedSelector\<object, TParentState\> | a selector for the entity's parent state |
| *extraInitialState?* | *any* | *the (optional) initial feature state as well as any additional state you may want to include for this entity * |

**Returns** [IModelState](#imodelstate)



<!-- -->
<br>
<br>
<!-- -->

<!-- Section: Decorators -->
## **Decorators**

`@Key` - Designates the key property for the entity.

{% code-tabs %}
{% code-tabs-item title="src/app/models/account.model.ts" %}
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
## **Facades**
Prefabricated facades come complete with a core set of properties and methods that may be used "out of the box" without any additional work on your part beyond extending the base facade class.

<br>

**Properties (Selectors)** <br>
Getters that will return an observable containing data on the **Facade**'s entities and state.

| Property | Return Type | Description |
| -------- | ----------- | ----------- |
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
| isLoading$ | Observable\<boolean\> | Get whether this **Entity** is loading. |
| isSaving$ | Observable\<boolean\> | Get whether this **Entity** is saving. |
| isDeleting$ | Observable\<boolean\> | Get whether this **Entity** is deleting. |
| loadedAt$ | Observable\<Date\> | Get the Date object representing when entities were loaded last. |
| savedAt$ | Observable\<Date\> | Get the Date object representing when entities were loaded last. |
| createdAt$ | Observable\<Date\> | Get the Date object representing when an entity was created last. |
| deletedAt$ | Observable\<Date\> | Get the Date object representing when an entity was deleted last. |

<br>

**Methods (Actions)** <br>
Functions for manipulating the **Facade**'s entities and state (note: all return types are void).

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
| edit | entity: Partial\<TModel\> | TBD |
| change | entity: Partial\<TModel\> | TBD |
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

<!-- -->
<br>
<br>
<!-- -->

<!-- Section: Interfaces -->
## **Interfaces**
This is by no means an exhaustive list of all interfaces included with the library, but it should have the most relevant ones.

<!-- IEntityState -->
<a id="ientitystate"></a>

<br>

`IEntityState<TModel>` - Structure for how entities are stored, including useful computed properties such as an array of their keys, status checks, timestamps, etc.

```typescript
interface IEntityState<TModel> {
  entities: IEntityDictionary<TModel>;
  ids: EntityIdentity[];
  currentEntityKey?: EntityIdentity;
  currentEntitiesKeys?: EntityIdentity[];
  editedEntity?: Partial<TModel>;
  isDirty?: boolean;
  currentPage?: Page;
  currentRange?: Range;
  totalPageableCount?: number;
  isLoading?: boolean;
  isSaving?: boolean;
  isDeleting?: boolean;
  loadedAt?: Date;
  savedAt?: Date;
  createdAt?: Date;
  deletedAt?: Date;
}
```

| Parameter | Description |
| --------- | ----------- |
| TModel | The model each entity follows (e.g. Customer, Account, Order, etc.). |


<!-- IModelState -->
<a id="imodelstate"></a>

<br>
<br>

`IModelState<TParentState, TState, TModel>` - Structure of the model state built by the `buildState()` function.

```typescript
interface IEntityState<TModel> {
  entities: IEntityDictionary<TModel>;
  ids: EntityIdentity[];
  currentEntityKey?: EntityIdentity;
  currentEntitiesKeys?: EntityIdentity[];
  editedEntity?: Partial<TModel>;
  isDirty?: boolean;
  currentPage?: Page;
  currentRange?: Range;
  totalPageableCount?: number;
  isLoading?: boolean;
  isSaving?: boolean;
  isDeleting?: boolean;
  loadedAt?: Date;
  savedAt?: Date;
  createdAt?: Date;
  deletedAt?: Date;
}
```

| Parameter | Description |
| --------- | ----------- |
| TParentState | TBD |
| TState | TBD |
| TModel | The model each entity follows (e.g. Customer, Account, Order, etc.). |