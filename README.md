# Describe Tree relations between hypermedia documents

An RDFS vocabulary to describe Tree-like relations between documents

## Why would anyone publish a tree structure over HTTP?

From the moment a document grows too large, we have been paginating it. This way, the browser only has to download more documents when the user is interested in them. However, for answering specific questions, pages (a one dimensional linked list structure) might not be the most efficient choice. Decades of computer science has showed us a plethora of tree structures that can be used to navigate big data sources.

Instead of paging the document, people have also been thinking about just exposing an API with plenty of features. Instead of traversing the tree on the server-side, the client thus now has to do more work, downloading more data and doing more of the processing. However, the data documents that need to be provided are always similar and thus caching works a lot better, providing your users with a similar user-perceived performance. We think we are hitting an interesting trade-off here for Open Data applications that cannot predict what questions will be asked on their interface.

Neat examples can be found here:
 * Autocompletion and geo-spatial search prototype: https://dexagod.github.io
 * Routable tiles for routing over a geospatially tiled road network:
   - The initial paper: http://pieter.pm/demo-paper-routable-tiles/
   - Calculating an isochrone demo with user-feedback while querying: http://hdelva.be/isochrone/demo.html

## The Vocabulary

Base URI to be used: `https://w3id.org/tree#`

Preferred prefixes: `tree:` or `tiles:` (the latter makes sense if you only use the geospatial tiling specific terms) 

The full vocabulary is explained in the [vocabulary.md](vocabulary.md).

## Application profiles

A couple of formal application profiles exist for specific use cases. Application profiles can be implemented by clients to understand specific hypermedia building blocks using the [vocabulary](vocabulary.md).

 * Building block 1: [Discovery](specs/1-discovery.md)
  - 1.1: discovering a tree:Node through hydra:view
  - 1.2: discovering a page is part of a larger hydra:Collection through dcterms:isPartOf (and then building block 1 can be used again)
 * Building block 2: [Traversing](specs/2-traversing.md) a tree:Node’s tree:hasChildRelation for more specific information
 * Building block 3: [Search forms](specs/3-search.md)
  - 3.1: using a search form for geospatial tiles cfr. OpenStreetMap tiles

See the [specs](specs/) folder for more information.

### Example Use

When dereferencing a specific Fragment (e.g., a fictional `http://api.example.com/stations/ge.jsonld`) with nodes, this should happen:

```turtle
@prefix tree: <https://w3id.org/tree#>.
@prefix hydra: <http://www.w3.org/ns/hydra/core#>.
#### This is the current document’s URL, typed a tree:Node
<http://api.example.com/stations/ge.jsonld> a tree:Node ;
    hydra:totalItems 100;
    tree:value "ge";
    tree:hasChildRelation _:b0, <...>;
    hydra:member <...>,<...>,<...> . # contains suggestions for "ge". If the number of distinct items equals the hydra:totalItems, this list is complete and no further children should be relaxed

#### This is a relation to a child being described. It has 1 or more compatible types that describes the relation with the parent’s value
_:b0 a tree:ChildRelation, tree:StringCompletesRelation;
    tree:child <http://api.example.com/stations/nt.jsonld> .

<http://api.example.com/stations/nt.jsonld> a tree:Node ;
    hydra:totalItems 100;
    tree:value "nt";
    hydra:member <...>,<...>,<...> . 
    
#### Also the main hydra collection is described
<http://api.example.com/stations> a hydra:Collection;
    hydra:manages gtfs:Stop;
    hydra:totalItems 660;
    hydra:member <...>,<...>,<...>; #may contain suggestions when no links have been followed so far.
    # This is a link to the root node, or already to multiple nodes. You can choose.
    hydra:view <http://api.example.com/stations/ge.jsonld>.
```
