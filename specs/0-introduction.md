# Collections # {#introduction}

<img src="https://docs.google.com/drawings/d/e/2PACX-1vTTCjBkBum1J4xgbg0oZJaD_H05dpZxhL6jrp1yzqoIsYw5EOa-7D24No_rfEyTipq1rLb-_tPTEYV0/pub?w=1093&amp;h=546" width="100%">

The TREE specification introduces these core concepts:
 * a `tree:Collection` is a collection of elements that adhere to a certain shape. It typically has these properties when described in a node:
     - `tree:member` indicates the object is a member of the collection
     - `tree:view` indicates a root node from where all members can be reached
     - `tree:shape` indicates the SHACL shape to which each member in the collection adheres
 * a `tree:Node`: is a page on which relations to other pages are described through the `tree:relation` predicate, and/or through which a next `tree:Node` can by found by using the `tree:search` form.
 * a `tree:Relation` is a relation from one node to another. An extension of this class indicates a specific type of relation (e.g., a `tree:GreaterThanRelation`). A relation typically has these properties:
     - a `tree:node` the URL of the other node
     - a `tree:path` indicating to which of the members' properties this relation applies
     - a `tree:value` indicating a value constraint on the members' values
     - a `tree:remainingItems` defining how many members can be reached when following this relation
     
# Imports # {#imports}

A `tree:import` can be defined on multiple levels. When defined as part of a `tree:Node`, this document always needs to be fetched when processing this Node.
When defined as part of the `tree:Relation`, one MUST fetch the import when the relation needs to be correctly evaluated (e.g., the resulting page contains elements without materialized WKT strings, which however can be fetched from the import).
When importing a file, no hypermedia relations will be followed from that import in order to get more data.

A `tree:importStream` can also be defined for providing a pubsub interface for subscribing to real-time updates. The object SHOULD be a [[!websockets]] or Server-Sent Events ([[!eventsource]]).

Instead of `tree:import`, one can also use `tree:conditionalImport` which links to an object of the type `tree:ConditionalImport` with these properties:
 * `tree:import` with a link to the page to import, or a `tree:importStream` with a pubsub stream to import (optionally)
 * `tree:path` with a property path for which this

Note: imports are powerful to keep recurring objects in a separate, more cacheable, resource.

No hypermedia controls in the body MUST be interpreted in the imported resource and the object must be fully contained within that information resource.

On the resources to import, Memento [[!RFC7089]] controls MAY be provided for historic versions.
