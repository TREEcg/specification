# Discovering a collection fragmented with TREE # {#discovering}

A `tree:Node` is a partial view of a `tree:Collection`.
Such a collection fragmented in nodes can be discovered when a page contains the predicate `tree:view`.
The object of the `tree:view` triple will contain a root node from which all elements in the collection can be retrieved.

When the current page is a `tree:Node`, there MUST be a property linking the current page URL to the URI of the `tree:Collection`. Three properties may be used:
 1. `<collection> tree:view <currentPageUrl> .`<br/>May be used *only* in the case when the entire `tree:Collection` can be found starting from this page.
 2. `<collection> void:subset <currentPageUrl> .`<br/>When the node is not a root node, but still a subset of the collection.
 3. `<currentPageUrl> dcterms:isPartOf <collection> .`<br/>The reverse property of 2.

Note: A `tree:Collection` is kept 1 on 1 compatible with the [Hydra Collections specification](https://www.hydra-cg.com/spec/latest/core/#collections). However, instead of `hydra:view`, we use `tree:view` and do not link to a `hydra:PartialCollectionView` but to a `tree:Node`.

Multiple views MAY be provided, and a TREE client MUST traverse all objects of `hydra:view` and `tree:view` linked to this particular collection.
Every entity linked from `tree:view` MUST be an entry point to retrieve **all** members of the collection.

## Multiple views ## {#multiple-views}

An effect for the client when you discovered a `tree:Collection` that can be viewed in different ways, is that you need to iterate through all of the views, and pick one. The one you pick can be based on a priority list indicating how well it suits your purpose. Mind that even if it does not serve your purpose well, following all `tree:Node`s might be faster thanks to the possible parallellization.

## DCAT compatibility ## {#dcat}

[[!VOCAB-DCAT-2]] is the standard for Open Data Portals by W3C. In order to find TREE compliant datasets in data portals, there SHOULD be a `dcat:accessURL` to the entrypoint where the `tree:Collection`s are described. Furthermore, there SHOULD be a `dct:conformsTo` this URI: `https://w3id.org/tree`.

<!--
## Compliance testing ## {#compliance}


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

You can test this query in the <a href="http://query.linkeddatafragments.org/#datasources=https%3A%2F%2Ftiles.openplanner.team%2Fplanet%2F14%2F8411%2F5485%2F&query=PREFIX%20dcterms%3A%20%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0APREFIX%20void%3A%20%3Chttp%3A%2F%2Frdfs.org%2Fns%2Fvoid%23%3E%0APREFIX%20hydra%3A%20%3Chttp%3A%2F%2Fwww.w3.org%2Fns%2Fhydra%2Fcore%23%3E%0A%0ASELECT%20*%20WHERE%20%7B%0A%20%20%20%20%3Fcollection%20void%3Asubset%7Chydra%3Aview%7C%5Edcterms%3AisPartOf%20%3FcurrentUrl%20.%0A%7D">Comunica Playground</a>.
-->
