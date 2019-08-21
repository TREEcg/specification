# Describe Tree relations between hypermedia documents

An RDFS vocabulary to describe Tree-like relations between documents. Weird nobody thought of this before.

## Why publish a hypermedia Tree?

When a document grows too large for 1 HTTP response, we need to fragment it. The way we fragment it will immediatly decide what queries will be fast and which queries will be slow.
Hypermedia trees are a tool to hit the sweet spot between data dumps and querying APIs (such as GraphQL or SPARQL). It is particularly a sweet spot for Open Data publishers that need a cost-efficient way of publishing their data, while allowing third parties to create serverless applications to reuse the data.

|   | dump  | tree fragments  | query  |
|:-:|:-:|:-:|:-:|
| processing | client | shared | server |
| server cost  | low  | okay  | high  |
|  client cost | high  | okay  | low  |
| caching | low | high | low |
| query execution control | high | high | low |

## The Vocabulary

Base URI to be used: `https://w3id.org/tree#`

Preferred prefixes: `tree:` or `tiles:` (the latter makes sense if you only use the geospatial tiling specific terms) 

The full vocabulary is explained in the [vocabulary.md](vocabulary.md).

For an overview see [treeontology.png](treeontology.png)

## Application profiles

A couple of formal application profiles exist for specific use cases. Application profiles can be implemented by clients to understand specific hypermedia building blocks using the [vocabulary](vocabulary.md).

 * Building block 1: [Discovery](specs/1-discovery.md)
     * 1.1: discovering a `tree:Node` through `hydra:view`
     * 1.2: discovering a page is part of a larger `hydra:Collection` through `dcterms:isPartOf` (and then building block 1 can be used again)
 * Building block 2: [Traversing](specs/2-traversing.md) a `tree:Node`’s `tree:hasChildRelation` for more specific information
 * Building block 3: [Search forms](specs/3-search.md)
      * 3.1: using a search form for geospatial tiles cfr. OpenStreetMap tiles
      * 3.2: using a search form to redirect to a Node containing the element

Mind that a server exposing data through the Tree Ontology __must__ [set the CORS headers](http://enable-cors.org) to allow any host.

In order to write a full Tree Ontology compliant client, you need to implement all building blocks, as well as the [Hydra partial collection view spec](). [Comunica](https://github.com/comunica/comunica) and its hypermedia actors (todo) is our main reference implementation.

See the [specs](specs/) folder for more information.

Different examples of datasets, implementing different mixes of building blocks, can be found in the [examples](examples/) folder.

### Implementations

Neat examples can be found here:

 * Autocompletion and geo-spatial search prototype: https://dexagod.github.io
 * Routable tiles for routing over a geospatially tiled road network:
     - The initial paper: http://pieter.pm/demo-paper-routable-tiles/
     - Calculating an isochrone demo with user-feedback while querying: http://hdelva.be/isochrone/demo.html

Also in the example folder in here, we’ve taken the effort to illustrate a couple of use cases:
 * [An ordered collection of pages](examples/paged-collection-with-order/)
 

