---
description: 'Own your app and do what you need, when you need'
---

# Taking Control

While we strive to provide as much commonly implemented functionality as we can, there can always be cases where you, the developer, must take control. Since this library is implemented much like any standard @ngrx application, it allows you to inject your own behavior whenever you need to. 

### Supporting Custom Behavior

If you require custom behavior for anything, you are always free to implement your own custom actions & custom effects. You are also free to hook into our library and framework by dispatching our actions from your own actions. 

This could, for example, allow you to implement a custom search behavior with your own `SearchEntityWhatever` action and `searchEntityWhatever$` effect, and dispatch the Auto-Entity relevant **success** and **failure** actions on response, which will be handled by our meta reducer. This would integrate a custom **initiating** action and data from a custom service call into Auto-Entity managed state. 

It is possible to integrate custom actions and effects in a variety of ways, allowing you to leverage the lower level power of NgRx when necessary, while still leveraging the ease of use offered by NgRx Auto-Entity. 

