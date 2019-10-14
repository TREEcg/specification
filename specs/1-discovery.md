# Discovering a tree:Node

This chapter elaborates on how you can discover a dataset that can be traversed using the Tree Ontology (see [2. Traversing](2-traversing.md) or which you can search through (see [3. Searching](3-search.md)).

## Formally

A `tree:Node` __must__ be made discoverable
 * Either as the object of a `hydra:view`
 * Or explicitly typed with a `hydra:Node`

For how to use or describe a `hydra:Collection`, we refer to the [Hydra Collections specification](https://www.hydra-cg.com/spec/latest/core/#collections). However: the object of the `hydra:view` is not a `hydra:PartialCollectionView`, but __must__ be a `tree:Node`.

Every `hydra:Collection` __should__ be annotated with [hydra:manages (see spec)](http://www.hydra-cg.com/spec/latest/core/#manages-block) to indicate what elements are maintained by the collection. 

Multiple views __may__ be provided, and a Tree client must traverse all objects of `hydra:view` linked to this particular collection. Every entity linked from `hydra:view` __must__ be an entry point to retrieve all members of the collection.

## User notes

### Note 1: Multiple views

An effect for the client when you discovered a `hydra:Collection` that can be viewed in different ways, is that you need to iterate through all of the views, and pick one. The one you pick can be based on a priority list indicating how well it suits your purpose. Mind that even if it does not serve your purpose well, following all `tree:Node`s might be faster thanks to the possible parallellization.

### Note 2: backward compatibility with hydra:PartialCollectionView

You _may_ provide backward compatibility with Hydra of your `tree:Node`, by also giving it the type `hydra:PartialCollectionView` and annotating it accordingly with `hydra:first`, `hydra:next`, etc. Mind however that hydra does not provide a way to describe the order of the collection.

An example is the simple [ordered pagination example in the examples folder](../examples/paged-collection-with-order).

### Note 3: DCAT compatibility

[DCAT](https://www.w3.org/TR/vocab-dcat-2/) is the standard for Open Data Portals by W3C. In order to find Tree Ontology compliant datasets, there _should_ be a `dcat:accessURL` to the entrypoint where the `hydra:Collection`s are described. Furthermore, there _should_ be a `dct:conformsTo` this URI: `https://w3id.org/tree`.

## Compliance testing

You can test compliance if the following query executed on your page gives a valid response. This query will detect all `tree:Node`s or `hydra:PartialCollectionView`s described in the page.

```sparql
PREFIX tree: <https://w3id.org/tree#>
PREFIX hydra: <http://www.w3.org/ns/hydra/core#>

CONSTRUCT {
   ?node a tree:Node .
   ?page a hydra:PartialCollectionView .
} WHERE {
   {
    ?collection hydra:view ?node .
    ?node tree:value ?value .
   } UNION {
    ?collection hydra:view ?page .
    MINUS { ?page tree:value ?value . }
   } UNION {
    ?page a hydra:PartialCollectionView .
   } UNION {
    ?node a tree:Node .
   }
}
```

[Test this query in the Comunica playground](http://query.linkeddatafragments.org/#datasources=https%3A%2F%2Fgraph.irail.be%2Fsncb%2Fconnections&query=PREFIX%20tree%3A%20%3Chttps%3A%2F%2Fw3id.org%2Ftree%23%3E%0APREFIX%20hydra%3A%20%3Chttp%3A%2F%2Fwww.w3.org%2Fns%2Fhydra%2Fcore%23%3E%0A%0ACONSTRUCT%20%7B%0A%20%20%20%3Fnode%20a%20tree%3ANode%20.%0A%20%20%20%3Fpage%20a%20hydra%3APartialCollectionView%20.%0A%7D%20WHERE%20%7B%0A%20%20%20%7B%0A%20%20%20%20%3Fcollection%20hydra%3Aview%20%3Fnode%20.%0A%20%20%20%20%3Fnode%20tree%3Avalue%20%3Fvalue%20.%0A%20%20%20%7D%20UNION%20%7B%0A%20%20%20%20%3Fcollection%20hydra%3Aview%20%3Fpage%20.%0A%20%20%20%20MINUS%20%7B%20%3Fpage%20tree%3Avalue%20%3Fvalue%20.%20%7D%0A%20%20%20%7D%20UNION%20%7B%0A%20%20%20%20%3Fpage%20a%20hydra%3APartialCollectionView%20.%0A%20%20%20%7D%20UNION%20%7B%0A%20%20%20%20%3Fnode%20a%20tree%3ANode%20.%0A%20%20%20%7D%0A%7D).
