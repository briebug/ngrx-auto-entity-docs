# Utility Functions

NgRx Auto-Entity ships with numerous utility functions to help you work with your auto entities. This includes utilities to retrieve the various entity names \(model, plural, and uri\), custom comparers, transforms, and more. 

Some utility functions require you to pass in the model type. This is the `class` of the model that defines the entity, rather than instances of that class. This is due to the fact that entity _metadata_ is attached to the model class via the `@Entity` and `@Key` decorators. Unless you explicitly convert your object instances to your model types using another utility function, generic plain old javascript objects will not directly include the necessary metadata required to determine much of the information these utility functions provide.



