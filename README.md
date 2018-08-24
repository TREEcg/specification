# Describe Tree relations between hypermedia documents

## What’s this?

_This_ is an RDFS vocabulary of HTTP URIs that can be used in any document that is able to contain RDF triples. The URIs describe the relation between pages.

## Why publishing trees over HTTP?

Web developers so far have been lazy. Mostly, they publish documents, and from the moment a document gets too big, they paginate the resource. Paging in Web APIs are common place, but a linear search in a Web API can be time consuming. Trees allow for making large quantities of data more accessible.

## Are you sure this is going to work?

Yeah! Trees are awesome. Exposing trees over HTTP really puts the control of which data to process when at the client-side, giving smart agents more flexibility with your data. For Open Data, this is just genius.

Instead of traversing the tree on the server-side, the client thus now has to do much work, downloading more data and doing more of the processing. However, the data documents that need to be provided are always similar and thus caching works a lot better, providing your users with a similar user-perceived performance.

Let’s provide you with a couple of examples:
 * Ternary search trie for autocompletion: https://codepen.io/pietercolpaert/pen/BxYQVQ?editors=1010
 * R-tree for finding bike stations in Flanders: _todo_
 * Autocompleting street names in Flanders: _todo_

## The Vocabulary

Base URI: https://w3id.org/tree#

Prefixes:

```turtle
@prefix tree: <https://w3id.org/tree#>.
@prefix foaf: <http://xmlns.com/foaf/0.1/>.
@prefix hydra: <http://www.w3.org/ns/hydra/core#>.
@prefix schema: <http://schema.org/>.
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.
```

### Classes

#### tree:Node

__subClassOf__: hydra:Collection, hydra:PartialCollectionView

A tree:Node is a node that may contain links to other dereferenceable resources, may contain the actual data (as part of a named graph, or using the predicate hydra:member).

#### tree:ChildRelation

An entity that describes a specific Parent-Child relation between two tree:Nodes.

The ChildRelation has specific sub-classes that implement a more specific type between the values. These types are described in the ontology (all classes are rdf:subClassOf tree:ChildRelation):
 - String, Date or Number comparison:
   - tree:StringCompletesRelation - The parent value needs to be concatenated with this node’s value
   - tree:GreaterThanRelation - the child is greater than the value. For string comparison, this relation can refer to a comparison configuration
   - tree:GreaterOrEqualThanRelation - similar to ↑
   - tree:LesserThanRelation
   - tree:LesserOrEqualThanRelation
   - tree:EqualThanRelation
 - Geo-spatial comparison (requires the node values to be WKT-strings): 
   - tree:GeospatiallyContainsRelation (for semantics, see [DE-9IM](https://en.wikipedia.org/wiki/DE-9IM))
 - Interval comparison
   - tree:InBetweenRelation
   
_Let us know in an issue if you want another type to be added to this official list_

### Properties

#### tree:childRelation

__Domain__: tree:Node
__Range__: tree:ChildRelation

#### tree:child

The parent node has a child with a certain relation (defined by tree:relationToParentValue). If the order of the children is important, use an rdf:List instead of using the property multiple times.

__Domain__: tree:ChildRelation
__Range__: tree:Node

#### tree:value

The contextual value of this node: may contain e.g., a WKT-string with the bound of a rectangle, may contain a string

__Domain__: tree:Node

## Specification

This is a specification on how clients are expected to find links in the tree. We will use a similar approach as with [Hydra Collections](https://www.hydra-cg.com/spec/latest/core/#collections)

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

The object of the `hydra:view` deviates from the Hydra specification. It is not a `hydra:PartialCollectionView`, but a `tree:Node`.

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
