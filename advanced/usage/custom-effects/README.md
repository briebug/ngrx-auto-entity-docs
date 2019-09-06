# Custom Effects

While NgRx Auto-Entity can do a lot for you, there may be times when you need to perform more complex operations than are supported with the ready-made generic actions of Auto-Entity. In such situations, you can always fall back onto "classic" NgRx and leverage all the raw power it provides. 

There are a few scenarios where you may need to fall back onto regular old NgRx. Purely custom functionality that doesn't fall into the "entity" mold may simply require a "full on NgRx." The top use case that comes to mind would be your average user login scenario, which often requires actions and server interactions that fall into more of a challenge/response sort of paradigm than an entity CRUD paradigm. 

### Purely Custom NgRx

In the auth case, aside from just `Load(User, id)` the actual act of Authentication will usually be a custom action, custom effect, probably a custom service method \(although it is fine to add additional functionality to entity services if you so require! Entity services may be provided directly as well as via the model if necessary.\)

### Composition or Bundling

There are a couple other scenarios where you may need to implement some custom functionality, however you may also still be able to leverage Auto-Entity to one degree or another. Such cases may include say a bundling complex behaviors such as a multi-entity save into a single action, and leveraging a custom effect to ultimately dispatch the necessary generic actions to work on each individual entity behind the scenes. 

### Workflows

Another case may be when you need a workflow, where one action begets another action, and so on. In this case custom effects may be leveraged to watch for particular generic actions dispatched for particular entities, and in turn dispatch other generic or custom actions for other entities or behaviors. This is where the power of reactive programming really comes into play.

