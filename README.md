![](tree-logo.svg)

__Exposing your Linked Datasets in hypermedia collections__

ᴛʀᴇᴇ enables you to describe relations between a specific value and all members a page linked from this current page.
Using this specific relation, a script or autonomous query client (such as [Comunica](https://comunica.linkeddatafragments.org/) and [Planner.js](https://planner.js.org)) can understand whether following the link will be useful or not.

Build the spec using [bikeshed](https://tabatkins.github.io/bikeshed/):
```bash
bikeshed watch spec.bs
```
The spec will be built [automatically](.travis.yml) when pushing to master.

## The Vocabulary

Base URI to be used: `https://w3id.org/tree#`.

Preferred prefix: `tree:`.

All newly introduced terms are explained in the [RDF vocabulary](vocabulary.md).

Most important concepts:
 * a `tree:Node` is a page that may contain members of a `tree:Collection`
 * a node has `tree:relation` entities with links to other nodes. This relation is typed (e.g., `tree:GeospatiallyContainsRelation` or a `tree:PrefixRelation`),
 * the relation has a `tree:value` and a `tree:path`. The former is a literal value on which the search term can be compared. The `tree:path` explains to which property of the members of the collection this relation applies.

## The specification

Available at https://treecg.github.io/specification

Mind that a server exposing data with ᴛʀᴇᴇ __must__ [set the CORS headers](http://enable-cors.org) to allow any host.
 
## Questions and Answers

### Why publish a hypermedia structure?

When a document grows too large for 1 HTTP response, we need to fragment it. The way we fragment it will immediatly decide what queries will be fast and which queries will be slow.
Hypermedia can be used to hit the sweet spot between data dumps and querying APIs (such as GraphQL or SPARQL). It is particularly a sweet spot for Open Data publishers that need a cost-efficient way of publishing their data, while allowing third parties to create serverless applications to reuse the data.

|   | dump  | ᴛʀᴇᴇ fragments  | query  |
|:-:|:-:|:-:|:-:|
| processing | client | shared | server |
| server cost  | low  | okay  | high  |
|  client cost | high  | okay  | low  |
| caching | low | high | low |
| query execution control | high | high | low |

### Why hypermedia?

When you write a client for one server, you can get away with hard-coding the way the API is built based on the API specification. When building a client for the entire Web, we need to make very general specifications that still allow our client to understand what it can do next. The latter are called the hypermedia controls.

### What are triples? JSON-LD? RDF? URIs? Linked Data?

Same idea as hypermedia, only for understanding the elements in the pages itself. See [these intro slides](https://speakerdeck.com/pietercolpaert/an-introduction-to-open-data), or [read this chapter](https://phd.pietercolpaert.be/chapters/data-and-interoperability).
