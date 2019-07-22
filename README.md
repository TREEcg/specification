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

## Application profiles

A couple of formal application profiles exist for specific use cases. Application profiles can be implemented by clients to understand specific hypermedia building blocks using the [vocabulary](vocabulary.md).

 * Building block 1: [Discovery](specs/1-discovery.md)
     * 1.1: discovering a `tree:Node` through `hydra:view`
     * 1.2: discovering a page is part of a larger `hydra:Collection` through `dcterms:isPartOf` (and then building block 1 can be used again)
 * Building block 2: [Traversing](specs/2-traversing.md) a `tree:Node`’s `tree:hasChildRelation` for more specific information
 * Building block 3: [Search forms](specs/3-search.md)
     * 3.1: using a search form for geospatial tiles cfr. OpenStreetMap tiles

Mind that a server exposing data through the Tree Ontology __must__ [set the CORS headers](http://enable-cors.org) to allow any host.

See the [specs](specs/) folder for more information.

### Example

When dereferencing a specific Fragment (e.g., a fictional `http://api.example.com/stations/ge.jsonld`) with nodes, this should happen:

```turtle
@prefix tree: <https://w3id.org/tree#>.
@prefix hydra: <http://www.w3.org/ns/hydra/core#>.
#### This is the current document’s URL, typed a tree:Node
<http://api.example.com/stations/ge.jsonld> a tree:Node ;
    dcterms:isPartOf <http://api.example.com/stations> ;
    tree:remainingItems 100;
    tree:value "ge";
    tree:hasChildRelation _:b0, <...> .

#### This is a relation to a child being described. It has 1 or more compatible types that describes the relation with the parent’s value
_:b0 a tree:ChildRelation, tree:StringCompletesRelation;
    tree:child <http://api.example.com/stations/nt.jsonld> .

<http://api.example.com/stations/nt.jsonld> a tree:Node ;
    tree:remainingItems 5;
    tree:value "nt" . 
    
#### Also the main hydra collection is described
<http://api.example.com/stations> a hydra:Collection;
    hydra:manages gtfs:Stop;
    hydra:totalItems 660;
    hydra:member <...>,<...>,<...>; #may contain suggestions when no links have been followed so far. 
    # This is a link to the root node, or already to multiple nodes. You can choose.
    hydra:view <http://api.example.com/stations/ge.jsonld>.
```

### Implementations

Neat examples can be found here:

 * Autocompletion and geo-spatial search prototype: https://dexagod.github.io
 * Routable tiles for routing over a geospatially tiled road network:
     - The initial paper: http://pieter.pm/demo-paper-routable-tiles/
     - Calculating an isochrone demo with user-feedback while querying: http://hdelva.be/isochrone/demo.html
