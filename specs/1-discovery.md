# Discovering a tree:Node

Every tree starts from a [Hydra Collection](https://www.hydra-cg.com/spec/latest/core/#collections). We extend this specification with the concept of a `tree:Node` (see user notes). This `tree:Node` is attached to a `hydra:view` in the Collection. Different `tree:Node`s and a `hydra:PartialCollectionView` can be added as multiple values of `hydra:view`s.

## Formally

A `tree:Node` __must__ be made discoverable as a `hydra:view` on a `hydra:Collection`. For how to use or describe a `hydra:Collection`, we refer to the [Hydra specification](https://www.hydra-cg.com/spec/latest/core/#x5-1-collections). However: the object of the `hydra:view` is not a `hydra:PartialCollectionView`, but a `tree:Node`.

Multiple views __may__ be provided, and a Tree client must traverse all objects of `hydra:view` linked to this particular collection. Every entity linked from `hydra:view` __must__ be an entry point to retrieve all members of the collection.

## User notes

Note 1: An effect for the client when you discovered a `hydra:Collection` that can be viewed in different ways, is that you need to iterate through all of the views, and pick one. The one you pick can be based on a priority list indicating how well it suits your purpose. Mind that even if it does not serve your purpose well, following all `tree:Node`s might be faster thanks to the possible parallellization.

## Compliance testing

You can test compliance if the following graphql-ld query gives a valid response:

```graphql
{
 //TODO
}
```
