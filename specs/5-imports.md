# Imports # {#imports}

A `tree:import` can be defined on multiple levels. When defined as part of a `tree:Node`, this document always needs to be fetched when processing this Node.
When defined as part of the `tree:Relation`, one MUST fetch the import when the relation needs to be correctly evaluated (e.g., the resulting page contains elements without materialized WKT strings, which however can be fetched from the import).
When importing a file, no hypermedia relations will be followed from that import in order to get more data.

A `tree:importStream` can also be defined for providing a pubsub interface for subscribing to real-time updates. The object SHOULD be a [[!websockets]] or Server-Sent Events ([[!eventsource]]).

Instead of `tree:import`, one can also use `tree:conditionalImport` which links to an object of the type `tree:ConditionalImport` with these properties:

 * `tree:import` with a link to the page to import, or a `tree:importStream` with a pubsub stream to import (optionally)
 * `tree:path` with a property path that indicates the resource that elements in the imported resource contain

Note: imports are powerful to keep recurring objects in a separate, more cacheable, resource.

No hypermedia controls in the body MUST be interpreted in the imported resource and the object must be fully contained within that information resource.

On the resources to import, Memento [[!RFC7089]] controls MAY be provided for historic versions.