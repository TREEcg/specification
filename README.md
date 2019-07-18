# Describe Tree relations between hypermedia documents

An RDFS vocabulary to describe Tree-like relations between documents

## Why would anyone publish a tree structure over HTTP?

From the moment a document grows too large, we have been paginating it. This way, the browser only has to download more documents when the user is interested in them. However, for answering specific questions, pages (a one dimensional linked list structure) might not be the most efficient choice. Decades of computer science has showed us a plethora of tree structures that can be used to navigate big data sources.

Instead of paging the document, people have also been thinking about just exposing an API with plenty of features. Instead of traversing the tree on the server-side, the client thus now has to do more work, downloading more data and doing more of the processing. However, the data documents that need to be provided are always similar and thus caching works a lot better, providing your users with a similar user-perceived performance. We think we are hitting an interesting trade-off here for Open Data applications that cannot predict what questions will be asked on their interface.

Neat examples can be found here:
 * Autocompletion and geo-spatial search: https://dexagod.github.io
 * Routable tiles for routing over a geospatially tiled road network: http://pieter.pm/demo-paper-routable-tiles/ 

## The Vocabulary

Base URI to be used: `https://w3id.org/tree#`

Preferred prefixes: `tree:` or `tiles:` (the latter makes sense if you only use the geospatial tiling specific terms) 

The full vocabulary is explained in the [vocabulary.md](vocabulary.md).

## Application profiles

A couple of formal application profiles exist for specific use cases. Application profiles can be implemented by clients to understand specific hypermedia building blocks using the [vocabulary](vocabulary.md).

 * Building block 1: discovering a tree:Node through hydra:view
 * Building block 2: discovering a page is part of a larger hydra:Collection through dcterms:isPartOf (and then building block 1 can be used again)
 * Building block 3: descending a tree:Node’s tree:hasChildRelation for more specific information
 * Building block 4: using a search form to jump to a specific Node (can also stand on its own)
  - 4.1: using a search form for geospatial tiles cfr. OpenStreetMap tiles

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

### 1. Discovering a tree

The tree must be made discoverable as a `hydra:view` on a `hydra:Collection`.

For how to use or describe a `hydra:Collection`, we refer to the Hydra specification: https://www.hydra-cg.com/spec/latest/core/#x5-1-collections

The object of the `hydra:view` deviates from the Hydra specification on collections. It is not a `hydra:PartialCollectionView`, but a `tree:Node`.

Multiple views may be provided, and a Tree client must traverse all objects of hydra:view linked to this particular collection. Every entity linked from hydra:view must be an entry point to retrieve all members of the collection.

### 2. Traversing tree:Node elements

When a `tree:Node` element is found, its `tree:value` must be set. The object of `tree:value` should be accompanied by a data type.

The `tree:Node` element may also have one or more `tree:childRelation`. A child relation is an entity of the type `tree:ChildRelation`, and may have one or more more specific types. A `tree:ChildRelation` must have one or more `tree:child` objects of the type `tree:Node`. In this description in all documents, this child must contain a `tree:value`. If a particular child is deemed interesting after evaluating the relation (see chapter 3), then this child’s URL needs to be dereferenced.

Every node may provide a `hydra:totalItems`, or a `hydra:manages`. A client may use `hydra:totalItems` and `hydra:manages` to estimate the completeness of the elements.

### 3. Handling tree:ChildRelation and its subclasses

When the _only_ type given for a certain ChildRelation is `tree:ChildRelation`, then the client must dereference all of its children.

Other types:
 - `tree:StringCompletesRelation` - In order to find a string, you must concatenate the value of this node with the value of the parent node, and its former parents that were linked through this relation.
 - `tree:GreaterThanRelation` and the likes - You must evaluate the value against the relation as defined by this relation. Number evaluation is straightforward. String comparisons will be further defined in Chapter 4.
 - Interval relations _TODO_ - see vocabulary for now
 - Geographic relations _TODO_ - see vocabulary for now

### 4. String comparisons

When comparing strings, different strategies can be applied. Bytestring or depending on a specific locale.

_TODO: define different strategies_
