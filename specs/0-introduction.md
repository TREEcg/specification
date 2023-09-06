# Overview # {#overview}

<img src="https://docs.google.com/drawings/d/e/2PACX-1vTTCjBkBum1J4xgbg0oZJaD_H05dpZxhL6jrp1yzqoIsYw5EOa-7D24No_rfEyTipq1rLb-_tPTEYV0/pub?w=1093&amp;h=546" width="100%">

The TREE specification introduces these core concepts:
 * a `tree:Collection` is a subclass of `dcat:Dataset`. The specialization is that it is a collection of members that MAY adhere to a certain shape. It typically has these properties when described in a node:
     - `tree:member` points at the first focus node from which to retrieve and extract all quads of a member.
     - `tree:view` points to a `tree:Node` from which all members can be reached.
     - `tree:shape` indicates the SHACL [[!SHACL]] shape to which each member in the collection adheres.
     - `tree:viewDescription` links to a description of the view (a `tree:ViewDescription`). Multiple descriptions MAY be provided that MUST be combined.
 * a `tree:Node`: is a page on which relations to other pages are described through the `tree:relation` predicate, and/or through which a next `tree:Node` can be found by using the `tree:search` form.
 * a `tree:Relation` is a relation from one node to another. An extension of this class indicates a specific type of relation (e.g., a `tree:GreaterThanRelation`). A relation typically has these properties:
     - a `tree:node` the URL of the other node
     - a `tree:path` indicating to which of the members' properties this relation applies
     - a `tree:value` indicating a value constraint on the members' values
     - a `tree:remainingItems` defining how many members can be reached when following this relation
 * a `tree:ViewDescription` is a subclass of `dcat:DataService` and serves a `tree:Collection`.
     - a `tree:search` describes a search form that allows an agent to jump to a specific `tree:Node`.

The first step when creating a TREE hypermedia interface is defining a collection of members:

<div class="example">
```
ex:Collection1 a tree:Collection;
               rdfs:label "A Collection of subjects"@en;
               tree:member ex:Subject1, ex:Subject2 .

ex:Subject1 a ex:Subject ;
            rdfs:label "Subject 1" ;
            ex:value 1 .

ex:Subject2 a ex:Subject ;
            rdfs:label "Subject 2" ;
            ex:value 2 .
```
</div>

From the moment this collection of members grows too big for one page, a fragmentation needs to be created in which an initial set of member can be found on an entry node, and more members can be found by interpreting the TREE _hypermedia controls_. This is illustrated by the next example:

<div class="example">
```
> HTTP GET https://example.org/Node1

ex:Collection1 a tree:Collection;
               tree:view ex:Node1 ;
               tree:member ex:Subject1, ex:Subject2 .

ex:Node1 a tree:Node ;
         tree:relation ex:R1,ex:R2 ;
         tree:viewDescription ex:ViewDescription1 .
 
ex:R1 a tree:GreaterThanOrEqualToRelation ;
      tree:node ex:Node3 ; # This is the URL of another page
      tree:value 3;
      tree:path ex:value .

ex:R1 a tree:LessThanRelation ; # This is very useful for a client that is looking for a value 10 or greater
      tree:node ex:Node3 ; # This is the URL of another page
      tree:value 10;
      tree:remainingItems 7 ;
      tree:path ex:value .

ex:R2 a tree:GreaterThanOrEqualToRelation ;
      tree:node ex:Node4 ; # This is the URL of another page
      tree:value 10;
      tree:remainingItems 10 ;
      tree:path ex:value .

ex:Subject1 a ex:Subject ;
            rdfs:label "Subject 1" ;
            ex:value 1 .

ex:Subject2 a ex:Subject ;
            rdfs:label "Subject 2" ;
            ex:value 2 .
```
</div>

<div class="informative">
Thanks to the [member extraction algorithm](#member-extraction-algorithm), a data publisher can choose to define their members in different ways:
 1. As in the examples above: all quads with the object of the `tree:member` quads as a subject (and recursively the quads of their blank nodes) are by default included (see also [CBD](https://www.w3.org/submissions/CBD/)), except when they would explicitely not be included in case 3, when the shape would be closed.
 2. Out of band / in band:
      - when no quads of a member have been found, the member will be dereferenced. This allows to publish the member on a separate page.
      - part of the member can be maintained elsewhere when a shape is defined (see 3)
 3. By defining a more complex shape with `tree:shape`, also nested entities can be included in the member
 4. By putting the triples in a named graph of the object of `tree:member`, all these triples will be matched. 
</div>

# Definitions # {#formalizations}

A `tree:Collection` is a set of `tree:Member`s. The set of members MAY be empty.

A `tree:Member` is a set of (at least one) quad(s) defined by the member extraction algorithm (next subsection).

A `tree:Node` is a dereferenceable resource of `tree:Relation`s and a subset of (`⊆`) members of the collection. In a `tree:Node`, both the set of `tree:Relation`s as the subset of members MAY be empty. The same member MAY be contained in multiple nodes.

A `tree:Relation` is a function denoting a conditional link to another `tree:Node`.

Note: The condition of multiple `tree:Relation`s to the same `tree:Node` MUST be combined with a logical AND.

A View is a specific set of interlinked `tree:Node`s, that together contain all members in a collection. A specific view will adhere to a certain growth or tree balancing strategy. In one view, completeness MUST be guaranteed.

A `tree:search` form is a IRI template, that when filled out with the right parameters becomes a `tree:Node` IRI, or when dereferenced will redirect to a `tree:Node` from which all members in the collection that adhere to the described comparator can be found.

# The member extraction algorithm # {#member-extraction-algorithm}

The first focus node is the object of the `tree:member` triple.
 1a. If a shape is set, [create a shape template](#shape-template) and execute the shape template extraction algorithm
 1b. If no shape was set, extract all quads with subject the focus node, and recursively include its blank nodes (see also [CBD](https://www.w3.org/submissions/CBD/))
 2. Extract all quads with the graph matching the focus node
 3. When no quads were extracted from 1 and 2, a client MUST dereference the focus node and re-execute 1 and 2.

## Shape Template extraction ## {#shape-template-extraction}

The Shape Template is a structure that looks as follows:

<div class="example">
```
class ShapeTemplate {
    closed: boolean;
    requiredPaths: Path[];
    optionalPaths: Path[];
    nodelinks: NodeLink[];
    atLeastOneLists: [ Shape[] ];
}
class NodeLink {
    shape: ShapeTemplate;
    path: Path;
}
```
</div>

Paths in the shape templates are [SHACL Property Paths](https://www.w3.org/TR/shacl/#property-paths).

A Shape Template has
 * __Closed:__ A boolean telling whether it’s closed or not. If it’s open, a client MUST extract all quads, after a potential HTTP request to the focus node, with subject the focus node, and recursively include its blank nodes (see also [CBD](https://www.w3.org/submissions/CBD/))
 * __Required paths:__ MUST trigger an HTTP request if the member does not have this path. All quads from paths, after a potential HTTP request, matching this required path MUST be added to the Member set.
 * __Optional paths:__ All quads from paths, after a potential HTTP request, matching this path MUST be added to the Member set.
 * __Node Links:__ A nodelink contains a reference to another Shape Template, as well as a path. All quads, after a potential HTTP request, matching this path MUST be added to the Member set. The targets MUST be processed again using the shape template extraction algorithm on that 
 * __atLeastOneLists__: Each atLeastOneList is an array of at least one shape with one or more required paths and atLeastOneLists that must be set. If none of the shapes match, it will trigger an HTTP request. Only the quads from paths matching valid shapes are included in the Member.

Note: Certain quads are going to be matched by the algorithm multiple times. Each quad will of course be part of the member only once.

This results in this algorithm:
 1. If it is open, a client MUST extract all quads, after a potential HTTP request to the focus node, with subject the focus node, and recursively include its blank nodes (see also [CBD](https://www.w3.org/submissions/CBD/))
 2. If the current focus node is a named node and it was not requested before:
    - test if all required paths are set, if not do an HTTP request, if they are set, then,
    - test if at least one of each list in the atLeastOneLists was set. If not, do an HTTP request.
 3. Visit all paths (required, optional, nodelinks and recursively the shapes in the atLeastOneLists if the shape is valid) paths and add all quads necessary to reach the targets to the result
 4. For the results of nodelinks, if the target is a named node, set it as a focus node and repeat this algorithm with that nodelink’s shape as a shape

### Generating a shape template from SHACL ### {#shacl-to-shape-template}

On a `tree:Collection`, a SHACL shape MAY be provided with the `tree:shape` property.
In that case, the SHACL shape MUST be processed towards a Shape Template as follows:

 1. Checks if the shape is deactivated (`:S sh:deactivated true`), if it is, don’t continue
 2. Check if the shape is closed (`:S sh:closed true`), set the closed boolean to true.
 3. All `sh:property` elements with an `sh:node` link are added to the shape’s NodeLinks array
 4. Add all properties with `sh:minCount` > 0 to the Required Paths array, and all others to the optional paths.
 5. Processes the [conditionals](https://www.w3.org/TR/shacl/#core-components-logical) `sh:xone`, `sh:or` and `sh:and` (but doesn’t process `sh:not`):
    - `sh:and`: all properties on that shape template MUST be merged with the current shape template
    - `sh:xone` and `sh:or`: in both cases, at least one item must match at least one quad for all required paths. If not, it will do an HTTP request to the current namednode.

Note: The way we process SHACL shapes into Shape Template is important to understand in order to know when an HTTP request will be triggered when designing SHACL shapes. A cardinality constraint not being exactly matched or a `sh:pattern` not being respected will not trigger an HTTP request, and instead just add the invalid quads to the Member. This is a design choice: we only define triggers for HTTP request from the SHACL shape to come to a complete set of quads describing the member the data publisher pointed at using `tree:member`.