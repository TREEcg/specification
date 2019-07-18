# Traversing Node elements

When a `tree:Node` element is found, its `tree:value` must be set. The object of `tree:value` should be accompanied by a data type.

The `tree:Node` element may also have one or more `tree:childRelation`. A child relation is an entity of the type `tree:ChildRelation`, and may have one or more more specific types. A `tree:ChildRelation` must have one or more `tree:child` objects of the type `tree:Node`. In this description in all documents, this child must contain a `tree:value`. If a particular child is deemed interesting after evaluating the relation (see chapter 3), then this child’s URL needs to be dereferenced.

Every node __may__ provide a `hydra:totalItems`, or a `hydra:manages`. A client __may__ use `hydra:totalItems` and `hydra:manages` to estimate the completeness of the elements.
