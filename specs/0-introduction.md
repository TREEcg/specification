# Collections # {#introduction}

<img src="https://docs.google.com/drawings/d/e/2PACX-1vTTCjBkBum1J4xgbg0oZJaD_H05dpZxhL6jrp1yzqoIsYw5EOa-7D24No_rfEyTipq1rLb-_tPTEYV0/pub?w=1093&amp;h=546" width="100%">

The TREE specification introduces these core concepts:
 * a `tree:Collection` is a subclass of `dcat:Dataset`. The specialization is that it is a collection of members that MAY adhere to a certain shape. It typically has these properties when described in a node:
     - `tree:member` indicates the object is a member of the collection
     - `tree:view` indicates a root node from where all members can be reached
     - `tree:shape` indicates the SHACL [[!SHACL]] shape to which each member in the collection adheres
 * a `tree:Node`: is a page on which relations to other pages are described through the `tree:relation` predicate, and/or through which a next `tree:Node` can be found by using the `tree:search` form.
 * a `tree:Relation` is a relation from one node to another. An extension of this class indicates a specific type of relation (e.g., a `tree:GreaterThanRelation`). A relation typically has these properties:
     - a `tree:node` the URL of the other node
     - a `tree:path` indicating to which of the members' properties this relation applies
     - a `tree:value` indicating a value constraint on the members' values
     - a `tree:remainingItems` defining how many members can be reached when following this relation
