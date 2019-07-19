# Discovering a tree:Node

Every tree starts from a [Hydra Collection](https://www.hydra-cg.com/spec/latest/core/#collections). We extend this specification with the concept of a `tree:Node` (see user notes). This `tree:Node` is attached to a `hydra:view` in the Collection. Different `tree:Node`s and differently ordered `hydra:PartialCollectionView`s can be added as a different `hydra:view`s.

## Formally

The tree __must__ be made discoverable as a `hydra:view` on a `hydra:Collection`. For how to use or describe a `hydra:Collection`, we refer to the [Hydra specification](https://www.hydra-cg.com/spec/latest/core/#x5-1-collections). However: the object of the `hydra:view` is not a `hydra:PartialCollectionView`, but a `tree:Node`.

Multiple views __may__ be provided, and a Tree client must traverse all objects of `hydra:view` linked to this particular collection. Every entity linked from `hydra:view` __must__ be an entry point to retrieve all members of the collection.

## User notes

Note 1: An effect for the client when you discovered a `hydra:Collection` that can be viewed in different ways, is that you need to iterate through all of the views, and pick one. The one you pick can be based on a priority list indicating how well it suits your purpose. Mind that even if it does not serve your purpose well, following all `tree:Node`s might be faster thanks to parallellism.

Note 2: Every `tree:Node` can be seen as a new `hydra:Collection` in its own respect. When a `hydra:totalItems` (which is related to a Collection) is mentioned, it points to the total number of items in this node plus all of its children. While the tree:Node described the entity of the current node, it does embody the collection of its own members and the members of its children. The number of items contained within the node itself 

## Compliance testing

You can test compliance if the following graphql-ld query gives a valid response:

```graphql

```
