# Collections # {#introduction}

The TREE specification introduces these core concepts:
 * a `tree:Collection` is a collection of elements that adhere to a certain shape. It typically has these properties when described in a node:
     - `tree:member` indicates the object is a member of the collection
     - `tree:view` indicates a root node that may have multiple relations
     - `tree:shape` indicates the SHACL shape to which each element in the collection adheres
 * a `tree:Node`: is a page on which relations to other pages are described through the `tree:relation` predicate.
 * a `tree:Relation` is a relation from one node to another. An extension of this class indicates a specific type of relation (e.g., a `tree:GreaterThanRelation`). A relation typically has these properties:
     - a `tree:path` indicating the resource in the elements on which the relation applies
     - the `tree:remainingItems` defining how many items can be downloaded when following this relation
     - a `tree:value` indicating a value that can be compared
     - a `tree:node` with the URL that can be fetched when this relation is not pruned by a search algorithm

# Imports # {#imports}

A `tree:import` can be defined on multiple levels. When defined as part of a `tree:Node`, this document always needs to be fetched when processing this Node.
When defined as part of the `tree:Relation`, one MUST fetch the import when the relation needs to be correctly evaluated (e.g., the resulting page contains elements without materialized WKT strings, which however can be fetched from the import).
When importing a file, no hypermedia relations will be followed from that import in order to get more data.

A `tree:importStream` can also be defined for providing a pubsub interface for subscribing to real-time updates. The object is supposed to be a [[!websockets]] or Server-Sent Events ([[!eventsource]]).

Instead of `tree:import`, one can also use `tree:conditionalImport` which links to an object of the type `tree:ConditionalImport` with these properties:
 * `tree:import` with a link to the page to import, or a `tree:importStream` with a pubsub stream to import (optionally)
 * `tree:path` with a property path for which this

Note: imports are powerful to keep recurring objects in a separate, more cacheable, resource.
