---
description: 'Clearing, Selecting & Deselecting, Oh my!'
---

# Utility Actions

Beyond the core CURDL actions, Auto-Entity also provides a few utility actions that cover other common entity-state related functionality. This includes an action to clear the state for a given model, as well as select and deselect a particular entity for a given model by reference or key. 

Recently added in v0.2 is the ability to select and deselect a set of entities for a given model by references or keys.

{% hint style="success" %}
* **Clear**: Empties the state for a given model
* **Select**: Select the given single entity
* **SelectByKey**: Select an entity who's key matches the specified key
* **SelectMany**: Select the specified entities as a set \*
* **SelectManyByKeys**: Select entities who's keys match the specified keys as a set \*
* **Deselect**: Deselect any previously selected single entity
* **DeselectMany**: Deselect the specified entities from current entity set
* **DeselectManyByKeys**: Deselect entities who's keys match the specified keys
* **DeselectAll**: Deselect all entities in current entity set
{% endhint %}

### Dealing with Entity Keys

For selecting entities by key, if an entity uses a composite key we provide some useful utility functions for retrieving an entities key. For more information about how composite keys are generated in Auto-Entity, read the advanced documentation on models and keys.

{% hint style="info" %}
When selecting, it should be understood that single entity selections and multiple entities selected as a set are tracked separately. It is possible to select a single entity, as well as select a set of entities, concurrently. Deselecting a single entity does not deselect the set, nor does deselecting a set deselect the single entity.

This in turn allows a simple hierarchical selection capability, wherein a subset of entities may be selected, then a single entity within the subset could be highlighted. It is also possible for the two selections to be entirely disjoint and unrelated. 
{% endhint %}

