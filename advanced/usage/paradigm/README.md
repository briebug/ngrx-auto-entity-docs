---
description: Learn about the required paradigm changes for Auto-Entity
---

# Paradigm Changes

For NgRX Auto-Entity to function properly, some minor changes will be required in the way you implement a couple standard bits of code. This includes service implementations and model implementations. These changes are not particularly significant, and for services we provide a lot of meta information to assist you in achieving whatever is necessary for your applications.

As models and services are standard elements of any Angular application, there should be no additional boilerplate code to implement here. These would be implemented regardless. However, be aware of the nuanced changes in how the model and service are used, and the explicit interface requirement for the service.

More significant changes occur when utilizing facades to interact with state. Facades encapsulate the complexities of @ngrx, presenting you with a simplified, logical and more standard API into working with your stateful data. 

