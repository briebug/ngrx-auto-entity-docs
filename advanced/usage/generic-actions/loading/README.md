---
description: 'One,All, Many, Pages, Ranges...'
---

# Loading Actions

The generic actions in the NgRx Auto-Entity library cover all the CRUD bases, as well as a range of loading options, plus some extra useful "utility" actions to cover other common state-related entity tasks. First up, we provide several options for loading data:

{% hint style="success" %}
* **Load**: one entity at a time
* **LoadAll**: every entity all at once
* **LoadMany**: lots of entities in arbitrary bunches
* **LoadPage**: entities in discrete bunches
* **LoadRange**: entities in sequential bunches
{% endhint %}

### Semantics & Behavior

Each load action is imbued with unique semantics and behavioral implications. For example, dispatching a `LoadAll` action implies that you wish to replace any previously existing entity state for the given model with whatever set of entities is retrieved by the entity service for that model. 

Dispatching `LoadMany` on the other hand implies that you wish to keep any previously existing entity state for the given model, and merge in whatever set of entities is retrieved. Similarly, `Load` will also merge the retrieved entity into any previously existing state.

### Pages vs. Ranges

Further, pages are semantically different than ranges. A page is a discrete slice of a set of entities with a distinct beginning and end. A range on the other hand is a sequential slice of a set of entities that ultimately form a continuous range. When dispatching `LoadPage` the implication is that you wish to replace any previously existing state for the given model. 

The explicit use case here is when the entire set of all entities for a given model is simply too large to fit in memory in a browser \(i.e. you may have tens of thousands...millions...billions of records in a database.\) Standard case for tables with paging controls. 

When dispatching `LoadRange` on the other hand, the implication is that you wish to join newly loaded ranges of entities onto existing state for the given model. The primary use case here is infinite scrolling, where additional entities are added to previously loaded entities in response to continued vertical scrolling or horizontal panning by a user. 

