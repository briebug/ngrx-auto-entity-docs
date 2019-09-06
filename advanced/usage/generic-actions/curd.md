---
description: 'Create, Update, REPLACE & Delete'
---

# CURD Actions

First off, you may be wondering why I am trying to coin a new term here. CURD? "Ain't it supposed to be CRUD?!" you say? Just wait till you hear the full "new" acronym! CURDL! Ah, the cow jokes that could be made... Well, **CURDL**, because: **C**reate, **U**pdate, **R**eplace, **D**elete & **L**oad! These are the core actions supported by Auto-Entity, and they span the range of common entity-related behaviors. 

### CURD, not CRUD??

CRUD in the past has stood for **C**reate, **R**ead, **U**pdate, **D**elete. This was a fairly standard term, however one of the goals of NgRx Auto-Entity is to fully support the entire range of HTTP/REST methods currently standardized. This includes both PUT and PATCH, which now have the semantics of "Replace in Whole" vs. "Update in Part." Auto-Entity provides actions to support both methods concurrently, required.

{% hint style="success" %}
* **Create**: Create a single entity \[POST\]
* **CreateMany**: Create multiple entities \(batch\) \[POST\]
* **Update**: Update a single entity \[PATCH\]
* **UpdateMany**: Update multiple entities \(batch\) \[PATCH\]
* **Replace**: Replace a single entity \[PUT\]
* **ReplaceMany**: Replace many entities \(batch\) \[PUT\]
* **Delete**: Delete a single entity \[DELETE\]
* **DeleteMany**: Delete many entities \(batch\) \[DELETE\]
{% endhint %}

### Bulk Actions Supported

Aside from supporting a full set of CURD actions for singular entities, we also support batch/bulk CURD actions allowing the creation, update/replacement and deletion of multiple entities at once. 

