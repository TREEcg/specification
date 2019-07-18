# Discovering a tree:Node

Every application profile starts from a [Hydra Collection](https://www.hydra-cg.com/spec/latest/core/#collections). We extend this specification with the concept of a `tree:Node` (see side note 2). This `tree:Node` is attached to a `hydra:view` in the Collection. Different `tree:Node`s and differently ordered `hydra:PartialCollectionView`s can be added as a different `hydra:view`s.

Side note 1: An effect for the client when you discovered a `hydra:Collection` that can be viewed in different ways, is that you need to iterate through all of the views, and pick one. The one you pick can be based on a priority list indicating how well it suits your purpose. Mind that even if it does not serve your purpose well, following all `tree:Node`s might be faster thanks to parallellism.

Side note 2: Every `tree:Node` can be seen as a new `hydra:Collection` in its own respect. When a `hydra:totalItems` (which is related to a Collection) is mentioned, it points to the total number of items in this node plus all of its children. While the tree:Node described the entity of the current node, it does embody the collection of its own members and the members of its children. The number of items contained within the node itself 

## Example 1:
