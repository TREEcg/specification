# Discovering a tree:Node

This chapter elaborates on how you can discover a dataset that can be traversed using the Tree Ontology (see [2. Traversing](2-traversing.md) or which you can search through (see [3. Searching](3-search.md)).

## Formally

A `tree:Node` __must__ be made discoverable as a `hydra:view` on a `hydra:Collection`. For how to use or describe a `hydra:Collection`, we refer to the [Hydra Collections specification](https://www.hydra-cg.com/spec/latest/core/#collections). However: the object of the `hydra:view` is not a `hydra:PartialCollectionView`, but a `tree:Node`.

Every hydra:Collection __must__ be annotated with [hydra:manages (see spec)](http://www.hydra-cg.com/spec/latest/core/#manages-block) to indicate what elements are maintained by the collection. 

Multiple views __may__ be provided, and a Tree client must traverse all objects of `hydra:view` linked to this particular collection. Every entity linked from `hydra:view` __must__ be an entry point to retrieve all members of the collection.

## User notes

### Note 1: Multiple views

An effect for the client when you discovered a `hydra:Collection` that can be viewed in different ways, is that you need to iterate through all of the views, and pick one. The one you pick can be based on a priority list indicating how well it suits your purpose. Mind that even if it does not serve your purpose well, following all `tree:Node`s might be faster thanks to the possible parallellization.

### Note 2: backward compatibility with hydra:PartialCollectionView

You _may_ provide backward compatibility with Hydra of your `tree:Node`, by also giving it the type `hydra:PartialCollectionView` and annotating it accordingly with `hydra:first`, `hydra:next`, etc. Mind however that hydra does not provide a way to describe the order of the collection.

An example is the simple [ordered pagination example in the examples folder](../examples/paged-collection-with-order).

### Note 3: DCAT compatibility

[DCAT](https://www.w3.org/TR/vocab-dcat-2/) is the standard for Open Data Portals by W3C. In order to find Tree Ontology compliant datasets, there _should_ be a `dcat:accessURL` to the entrypoint where the `hydra:Collection`s are described. Furthermore, there _should_ be a `dct:conformsTo` this URI: `https://w3id.org/tree/`.

## Compliance testing

You can test compliance if the following graphql-ld query gives a valid response:

```graphql
{
  view { TODO }
  TODO
}
```
