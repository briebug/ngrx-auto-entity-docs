---
description: Simplifying Reactive State!
---

# NgRx Auto-Entity

![](.gitbook/assets/ngrx-ae-logo-light.png)

**NgRx Auto-Entity** is a library that _simplifies reactive state_ in `@ngrx` with reusable generic actions, automated effects & reducers and _**prefabricated facades.**_

## What is NgRx Auto-Entity?

NgRX Auto-Entity is an add-on library for `@ngrx` that aims to greatly simplify use of `@ngrx` and reduce the implementation load of adding `@ngrx` to your application. We provide a set of ready-made, generic actions that cover most of the common use cases such as loading entities and sets of entities as well as creating, updating or replacing, and deleting entities. 

Auto-Entity wires in _**ready-made**_ _effects_ to handle standard behaviors, as well as provides a core _**meta reducer**_ that _reduces_ _all_ entity state managed by this library. For most use cases, the implementation burden for you the developer will be limited to basic initial `@ngrx` setup, creation of models for each entity & creation of services for each entity. This is functionality that would need to be implemented by you the developer regardless. 

Where we save you time is by eliminating the need to implement unique sets of **CRUD** actions for each and every entity, as well as the side effects that handle standard **CRUD** behavior for each and every entity. This can be a significant amount of development effort for each `@ngrx` application, and we hope the savings we offer will allow you to focus on solving the critical business needs of your customers. 

Further, Auto-Entity generates pre-fabricated **Facades** around your entity state, allowing you to _extract_ all `@ngrx` and other state related code from your components, _encapsulate_ them in a facade layer that presents a much _simpler_, more _logical_ and easier to comprehend and use API for your entities.



