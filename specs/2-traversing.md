# Traversing Node elements

A `tree:Node` element __may__ have one or more `tree:relation` properties. A relation is an entity of the type `tree:Relation`, and __may__ have a more specific types. A `tree:Relation` __must__ have one `tree:node` object of the type `tree:Node`. By default, all nodes need to be followed, unless the client flags this relation for pruning.

The `tree:Relation`’s `tree:value` __must__ be set. The object of `tree:value` __should__ be accompanied by a data type when it is a literal value.

Every `tree:Relation` __should__ have a `shacl:path`, indicating on what exact triple(s) the `tree:value` applies. For the different ways to express or handle a `shacl:path`, we refer to [2.3.1 in the shacl specification](https://www.w3.org/TR/shacl/#x2.3.1-shacl-property-paths). Mind that all possible combinations of e.g., `shacl:alternativePath` or `shacl:inversePath` may be used. When `sh:alternativePath` is used, the order in the list will define the importance of the order when evaluating the `tree:Relation`. If no `shacl:path` is provided in this document, the `tree:value` must be interpreted as filtering all objects that can be compared against the `tree:value`.

If a `shacl:path` is defined, and
 1. If no `hydra:member` relations are defined, the `shacl:path` needs to be evaluated on all triples in the page
 2. If a `hydra:member` relationship is defined, the `shacl:path` should only start from the member URI
<!-- MAYBE 3. For quad representations, you can find the triple on which the `shacl:path` should be evaluated by adding the graph name as an object of `tree:memberGraph` to the `hydra:Collection`. #PC: I’m unsure why to add this. I think it only adds complexity without adding real benefits to the data model, serialization, bandwidth, query performance, etc.-->

The result of the evaluation of the `shacl:path`, is the value on which the `tree:value` is based.

When no `shacl:path` is defined, the `tree:value` applies to the `hydra:member` elements, or all of the triples their objects given in the page when no `hydra:member` is available. If due to `rdfs:range` incompatibility, the object cannot be compared, the triple automatically becomes not part of the comparison.

__Informative note__: Not having a `hydra:member` or `shacl:path` may be useful for triple-based indexes such as [Triple Pattern Fragments](https://www.hydra-cg.com/spec/latest/triple-pattern-fragments/). In order to support metadata about the triples itself, something like [RDF*](http://blog.liu.se/olafhartig/tag/rdf-star/) would be needed.

Every node __may__ provide a `tree:remainingItems`. A client __may__ use `tree:remainingItems` to estimate the completeness of the downloaded elements to the end-user.

__Informative note__: A client needs to keep a list of already visited pages, as despite this being the Tree Ontology, circular references and back-links are allowed.

## Relation

When the _only_ type given for a certain Relation is `tree:Relation`, then the client must dereference all of the nodes. While this may seem useless, it can be used for the same use case as a `hydra:PartialCollectionView`.

For other types: see [vocabulary](../vocabulary.md) for now.

### Comparing strings according to a locale

When comparing strings, different strategies can be applied. Bytestring or depending on a specific locale.

_TODO: define different strategies_

<!-- This now became quite clear without extra spec text
# Traversing geospatial tiles

When a tile is found through tree:latitudeTile, tree:longitudeTile and tree:zoom, other elements can be found in two ways:
 1. Through a description of other elements in the collection
 2. A search form is exposed and all other URLs to tiles in a viewport or bounding box can be calculated

This text is for the first option. See the [Search spec](3-search.md) for the second option.
-->

# Compliance testing

You can test compliance if the following query executed on your page gives a valid response. You need the current page URL and bind this to `?url`.

## Finding relations

```sparql
SELECT ?relationType ?value ?node
WHERE {
  ?collection void:subset|hydra:view|^dcterms:isPartOf <page_url> ;
              tree:relation ?relation .
  ?relation a ?relationType ;
            tree:value ?value ;
            tree:node ?node .
}
```

## Finding all elements in the current page

```sparql
CONSTRUCT {
  ?s ?p ?o .
} WHERE {
  ?collection void:subset|hydra:view|^dcterms:isPartOf <page_url> ;
              hydra:member ?s .
  ?s ?p ?o .
}
```
