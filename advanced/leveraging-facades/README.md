---
description: Ultimate power at your fingertips!
---

# Leveraging Facades

Prefabricated facades are one of NgRx Auto-Entities most powerful offerings. Not only can they greatly simplify your code, facades offer a way of organizing and encapsulating core business logic. You can pull a lot of behavior out of your components and move it into your facades. This simplifies your components, and can potentially improve code reuse. 

### Generated for You

When preparing your state with a call to the `buildState` function, Auto-Entity will generate an entity facade class for you. These classes are best considered to be base classes, from which you should extend another class. See the instructions on how in the next section. 

### Rich Prefabricated Functionality

Prefabricated facades come complete with a core set of properties and methods that may be used "out of the box" without any additional work on your part beyond extending the base facade class. There is no explicit need to wrap every property or method provided by a facade base class in your own class in order to use it. You may, and are encouraged to, add your own custom properties and methods to encapsulate higher level behavior, though!

