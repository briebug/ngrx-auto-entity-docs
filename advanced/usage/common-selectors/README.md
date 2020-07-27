# Common Selectors

Much like @ngrx/entity, when you build state for a new entity with NgRx Auto-Entity we provide a set of ready-made common selectors. These selectors allow you to retrieve the state Auto-Entity manages for you.

{% hint style="info" %}
We have attempted to maintain compatibility with @ngrx/entity here, so we include the core standard set of selectors that are provided by that framework.
{% endhint %}

{% hint style="success" %}
To name a few...

* selectAll
* selectEntities
* selectIds
* selectTotal
* ...
{% endhint %}

These selectors are returned from our `buildState` utility function as a `ISelectorMap<TParentState, TModel>`. See the reference documentation for the full interface definition and the complete list of selectors.

