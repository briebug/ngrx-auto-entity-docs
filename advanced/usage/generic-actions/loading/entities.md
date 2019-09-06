# Loading Entities

NgRx Auto-Entity offers many ways to load data. The most common of these will usually be the single, all and many loads. A single entity load is pretty strait forward, requiring that you provide a model type and entity key to the `Load` action. This will retrieve the single entity uniquely identified by the specified key and **merge** it into the state for that entity.

{% hint style="info" %}
There are several terms I will use throughout this documentation when referring to what kind of changes are performed on state as a result of an action. 

* **Merged**: State that is merged will add or update existing entities in state with entities of the action being reduced
* **Replaced**: State that is replaced will drop any existing state in favor of the entities of the action being reduced
* **Concatenated**: State where the entities of the action are simply added to whatever is already in state
{% endhint %}

### Loading Everything

Loading all entities of a given type is even more strait forward, as you simply provide the model type to the `LoadAll` action. This will retrieve every entity of a given type and **replace** it in the state for that entity.

### Loading Many Things

The third common way of loading entities is to load many entities. This is also strait forward and relies on providing at least a model type to the `LoadMany` action. This will retrieve entities and **merge** them into the state for that entity. 

{% hint style="warning" %}
Note that when the behaviors of actions are described here, this is the expected behavior for a given action. Since implementation of Entity services is still the responsibility of the developer, it is up to the developer top ensure they conform to the expectations of each they are handling in their Entity services. 
{% endhint %}

### Custom Criteria

For many loads, you may need to specify criteria other than a primary or uniquely identifying key. All NgRx Auto-Entity actions support [optional custom criteria](../custom-criteria.md) that may be used for this purpose.

