# Traversing Node elements

When a `tree:Node` element is found, its `tree:value` must be set. The object of `tree:value` should be accompanied by a data type.

The `tree:Node` element may also have one or more `tree:childRelation`. A child relation is an entity of the type `tree:ChildRelation`, and may have one or more more specific types. A `tree:ChildRelation` must have one or more `tree:child` objects of the type `tree:Node`. In this description in all documents, this child must contain a `tree:value`. If a particular child is deemed interesting after evaluating the relation (see chapter 3), then this child’s URL needs to be dereferenced.

Every node __may__ provide a `hydra:totalItems`, or a `hydra:manages`. A client __may__ use `hydra:totalItems` and `hydra:manages` to estimate the completeness of the elements.


## ChildRelations

When the _only_ type given for a certain ChildRelation is `tree:ChildRelation`, then the client must dereference all of its children.

Other types:
 - `tree:StringCompletesRelation` - In order to find a string, you must concatenate the value of this node with the value of the parent node, and its former parents that were linked through this relation.
 - `tree:GreaterThanRelation` and the likes - You must evaluate the value against the relation as defined by this relation. Number evaluation is straightforward. String comparisons will be further defined in Chapter 4.
 - Interval relations _TODO_ - see vocabulary for now
 - Geographic relations _TODO_ - see vocabulary for now

### Comparing strings according to a locale

When comparing strings, different strategies can be applied. Bytestring or depending on a specific locale.

_TODO: define different strategies_
