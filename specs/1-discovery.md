# Discovering a tree:Node

This chapter elaborates on how you can discover a dataset that can be traversed using the TREE specification (see [2. Traversing](2-traversing.md) or which you can search through (see [3. Searching](3-search.md)).

## Formally

A `tree:Node` __must__ be made discoverable
 * Either as the object of a `tree:view`
 * Or explicitly typed with a `tree:Node`

A `tree:Collection` is kept 1 on 1 compatible with the [Hydra Collections specification](https://www.hydra-cg.com/spec/latest/core/#collections). However, instead of `hydra:view`, we use `tree:view` and do not link to a `hydra:PartialCollectionView` but to a `tree:Node`.

If the current page is a `tree:Node`, there __must__ be a property linking the current page URL to the URI of the `tree:Collection`. Three properties may be used:
 1. `<collection> tree:view <currentPageUrl> .`: may be used _only_ in the case when the _entire_ `tree:Collection` can be found starting from this page.
 2. `<collection> void:subset <currentPageUrl> .`
 3. `<currentPageUrl> dcterms:isPartOf <collection> .`

Multiple views __may__ be provided, and a TREE client must traverse all objects of `hydra:view` and `tree:view` linked to this particular collection. Every entity linked from `tree:view` __must__ be an entry point to retrieve __all__ members of the collection.

## User notes

### Note 1: Multiple views

An effect for the client when you discovered a `tree:Collection` that can be viewed in different ways, is that you need to iterate through all of the views, and pick one. The one you pick can be based on a priority list indicating how well it suits your purpose. Mind that even if it does not serve your purpose well, following all `tree:Node`s might be faster thanks to the possible parallellization.

### Note 2: DCAT compatibility

[DCAT](https://www.w3.org/TR/vocab-dcat-2/) is the standard for Open Data Portals by W3C. In order to find TREE compliant datasets, there _should_ be a `dcat:accessURL` to the entrypoint where the `tree:Collection`s are described. Furthermore, there _should_ be a `dct:conformsTo` this URI: `https://w3id.org/tree`.

## Compliance testing

You can test compliance if the following query executed on your page gives a valid response. This query will detect all `tree:Node`s described in the page.

```sparql
PREFIX tree: <https://w3id.org/tree#>

CONSTRUCT {
   ?node a tree:Node .
} WHERE {
   {
    ?collection tree:view ?node .
   } UNION {
    ?node a tree:Node .
   }
}
```

And this one to find discover whether the current page is part of one or more `tree:Collection`s.

```sparql
PREFIX void: <http://rdfs.org/ns/void#>
PREFIX hydra: <http://www.w3.org/ns/hydra/core#>
SELECT * WHERE {
    ?collection void:subset|hydra:view|^dcterms:isPartOf ?currentUrl .
}
```

You can test this query on the [Comunica Playground](http://query.linkeddatafragments.org/#datasources=https%3A%2F%2Ftiles.openplanner.team%2Fplanet%2F14%2F8411%2F5485%2F&query=PREFIX%20dcterms%3A%20%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0APREFIX%20void%3A%20%3Chttp%3A%2F%2Frdfs.org%2Fns%2Fvoid%23%3E%0APREFIX%20hydra%3A%20%3Chttp%3A%2F%2Fwww.w3.org%2Fns%2Fhydra%2Fcore%23%3E%0A%0ASELECT%20*%20WHERE%20%7B%0A%20%20%20%20%3Fcollection%20void%3Asubset%7Chydra%3Aview%7C%5Edcterms%3AisPartOf%20%3FcurrentUrl%20.%0A%7D).
