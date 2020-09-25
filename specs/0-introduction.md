# Overview # {#introduction}

**Namespace**: `https://w3id.org/tree#`

The TREE specification introduces these core concepts:
 * a `tree:Node`: is a page on which relations to other pages are described
 * a `tree:Relation` is a relation from one node to another. An extension of this class indicates a specific type of relation (e.g., a `tree:GreaterThanRelation`). A relation typically has these properties:
     - a `tree:path` indicating the resource in the elements on which the relation applies
     - the `tree:remainingItems` defining how many items can be downloaded when following this relation
     - a `tree:value` indicating a value that can be compared
     - a `tree:node` with the URL that can be fetched when this relation is not pruned by a search algorithm
 * a `tree:Collection` is a collection of elements that adhere to a certain shape. It typically has these properties when described in a node:
     - `tree:member` indicates the object is a member of the collection
     - `tree:view` indicates a root node that may have multiple relations
     - `tree:shape` indicate the SHACL shape to which each element in the collection adheres

A `tree:import` can be defined on multiple levels. When defined as part of a `tree:Collection`, this document always needs to be fetched when processing this collection.
When defined as part of the Relation, one needs to fetch the import when the relation needs to be correctly evaluated (e.g., the resulting page contains elements without materialized WKT strings, which however can be fetched from the import).
When importing a file, no hypermedia relations will be followed from that import in order to get more data.

A `tree:importStream` can also be defined on top of the Collection for providing a pubsub interface for subscribing to real-time updates.

## Qualified properties ## {#qualified}

Instead of `tree:import`, one can also use `tree:qualifiedImport` which links to an object of the type `tree:Import` with these properties:
 * `tree:import` with a link to the page to import
 * `tree:path` with a property path for which this 
 * `tree:importStream` with a pubsub stream to import

`tree:qualifiedValue` can also describe the value and identify it with a URI. This URI can be used by a caching mechanism. A qualifiedValue can also have a `tree:import` indicating a resource that must be fetched when evaluating it. The `tree:value` can then again be used to indicate the actual value, or have a `tree:path` indicating the value in this object to be compared.

ISSUE: how does cache invalidation work with `tree:qualifiedValue`?

## Fallbacks ## {#fallbacks}

In this chapter we define what a search algorithm should do when one of the properties has not been set.

No Collection and thus no member structure. Then the path needs to be evaluated on top of every subject in the page.

No `tree:path` given: evaluate the relation/value on every triple of the member, or when no collection/member is available, on every triple in the page.

No `tree:shape` given: then this source may contain any kind of triple, and thus this collection needs to be used to search for individual triples instead.
