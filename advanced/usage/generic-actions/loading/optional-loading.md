---
description: Only if necessary...
---

# Optional Loading

New in version 0.5 is the ability to "optionally" load data. This functionality was added with the IfNecessary set of loading actions. These actions will perform some basic checks against the existing state in your application, which necessitates that you actually expose your state to the library \(more on this in a moment.\) In essence, if the entities are already in state, the actual load action will not be dispatched. Optionally, a `maxAge` may be specified in the IfNecessary actions, or a `defaultMaxAge` may be defined on the model with the `@Entity` decorator. 

### Providing Your Store

In order for optional loading actions to function, they must be able to access **your** state. ****This means you must expose your store to the auto-entity library. This is achieved by providing the `NGRX_AUTO_ENTITY_APP_STORE` injection token with a factory function that will return your `Store` instance. 

```typescript
export function provideAppStore(store: Store<any>) {
  return store;
}

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, HttpClientModule, StateModule],
  providers: [
    { provide: Customer, useClass: EntityService },
    { 
      provide: NGRX_AUTO_ENTITY_APP_STORE, 
      useFactory: provideAppStore, 
      deps: [Store] 
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

There is not much to it, other than to create a factory function that depends on the NgRx `Store`, and to specify `Store` as a dependency of your provider. You should also provide this token in your root application module, to ensure it is within the root injection scope \(otherwise Auto-Entity will be unable to find it, as it is unable to use child injection scopes defined by your own applications.\)

### Using Optional Loading

Once you have provided your store to Auto-Entity, you may use the optional loading actions. These actions are available for each of the different loading actions that already exist:

{% hint style="success" %}
* LoadIfNecessary
* LoadAllIfNecessary
* LoadManyIfNecessary
* LoadPageIfNecessary
* LoadRangeIfNecessary
{% endhint %}

These actions will attempt to verify that the requested data already exists within state. For loading a single entity, it will look for that entity \(its key\) in state. For loading all or many entities, it will check if any entities are in state. \(Note: Currently, we will not try to verify if custom criteria matches, as auto-entity currently does not store the previous criteria. This may change in a future version of auto-entity.\) For loading a page or range, the current page/range criteria will be compared against existing state.

Optional loading is an additional "layer" on top of normal loading. When dispatching a `LoadIfNecessary` action, this action simply invokes an effect that performs the appropriate verification against state to determine if a `Load` action should be dispatched or not. If the entity or entities are found in state, then a `Load` action will NOT be dispatched. Otherwise, the `Load` action will be dispatched normally.

The `*IfNecessary` actions therefor only introduce new functionality, and otherwise do not change the existing load functionality. Any custom code that may rely on the existing loading mechanism in Auto-Entity to continue working the way it does will continue to work when using optional loading. You may also add effects that expect `*Success` or `*Failure` load result actions, as they will still be dispatched appropriately when using `*IfNecessary` actions. 

#### Maximum Age

When performing an optional load, a maximum age, in seconds, may be specified. If the last loadedAt timestamp for the entity in question is older than the specified age, then entities will be loaded. A maximum age may be specified within the `*IfNecessary` action as a parameter, or a default max age may be specified on the entity model using the `@Entity` decorator:

```typescript
@Entity({
  modelName: 'Customer',
  defaultMaxAge: 180
})
export class Customer {}
```

If both a default maximum age is specified on the model, and a maximum age is passed in the action, the action age takes precedence. Auto-Entity provides an enumeration of pre-defined ages, for convenience:

{% hint style="success" %}
* Minute 
* Hour
* QuarterDay
* HalfDay
* Day
* Week
{% endhint %}

The enum is numeric, and as such may be combined with basic math to produce N number of minutes, days, etc.:

```typescript
@Entity({
  modelName: 'Customer',
  defaultMaxAge: EntityAge.Minute * 3
})
export class Customer {}
```

