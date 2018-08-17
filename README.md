# Describe Tree relations between hypermedia documents

## What’s this?

_This_ is an RDFS vocabulary of HTTP URIs that can be used in any document that is able to contain RDF triples. The URIs describe the relation between pages

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

Base URI: https://w3id.org/tree/

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

A tree:Node is a node that may contain links to other dereferenceable resources, may contain the actual data (as part of a named graph, or using the predicate hydra:member).

### Properties

#### tree:hasRootNode

rdfs:subClassOf foaf:primaryTopic

__Domain__: hydra:Resource, foaf:Document
__Range__: tree:Node

#### tree:child

The parent node has a child with a certain relation (defined by tree:relationToParentValue). If the order of the children is important, use an rdf:List instead of using the property multiple times.

__Domain__: tree:Node
__Range__: tree:Node, rdf:List

#### tree:parent

Reverse property of child. Property is not used oftend, but here for the sake of completeness. We recommend to use tree:child as much as possible.

__Domain__: tree:Node
__Range__: tree:Node

#### tree:relationToParentValue

This property links to a way this Node relates to the parent in order to find the node(s) you are looking for. Recommended properties:
 - String, Date or integer comparison:
   - http://schema.org/greater
   - http://schema.org/greaterOrEqual
   - http://schema.org/lesser
   - http://schema.org/lesserOrEqual
   - http://schema.org/nonEqual (although we don’t see a practical use case)
   - http://schema.org/equal
   - [tree:stringCompletes](https://w3id.org/tree#stringCompletes) - the child value should be concatenated with the parent value
 - Geographic relation (then values need to be WKT strings)
   - http://schema.org/geospatiallyContains
   - Or others as defined by [DE-9IM](https://en.wikipedia.org/wiki/DE-9IM) and as used by [schema:Place](http://schema.org/Place).
 - Intervals (then values should be intervals)
   - InBetween _TODO_

_Let us know in an issue if you want another property to be added to this list_

#### tree:value

The contextual value of this node: may contain e.g., a WKT-string with the bound of a rectangle, may contain a string



__Domain__: tree:Node

__TODO__ How to know when you have to fetch a resource?

## Specification

This is a specification on how clients are expected to find links in the tree.

_TODO_
