# Overview # {#overview}

<img src="https://docs.google.com/drawings/d/e/2PACX-1vTTCjBkBum1J4xgbg0oZJaD_H05dpZxhL6jrp1yzqoIsYw5EOa-7D24No_rfEyTipq1rLb-_tPTEYV0/pub?w=1093&amp;h=546" alt="An overview of the TREE specification with the TREE collection, a reference to the first focus node of its members, and the relations to other nodes from the current node."/>

The TREE specification introduces these core concepts:
 * a <code>tree:Collection</code> is a subclass of <code>dcat:Dataset</code> ([[!vocab-dcat-3]]). The specialization being that it is a DCAT dataset a collection of members. It typically has these properties when described in a node:
     - <code>tree:member</code> points at the first focus node from which to retrieve and extract all quads of a member.
     - <code>tree:view</code> points to the current <code>tree:Node</code> you’re visiting.
     - <code>tree:shape</code> indicates the [[!SHACL]] shape to which each member in the collection adheres.
     - <code>tree:viewDescription</code> links to a description of the view (a <code>tree:ViewDescription</code>). Multiple descriptions MAY be provided that MUST be combined.
 * a <code>tree:Node</code>: is a page on which relations to other pages are described through the <code>tree:relation</code> predicate, and/or through which a next <code>tree:Node</code> can be found by using the <code>tree:search</code> form.
 * a <code>tree:Relation</code> is a relation from one node to another. An extension of this class indicates a specific type of relation (e.g., a <code>tree:GreaterThanRelation</code>). A relation typically has these properties:
     - a <code>tree:node</code> the URL of the other node
     - a <code>tree:path</code> indicating to which of the members' properties this relation applies
     - a <code>tree:value</code> indicating a value constraint on the members' values
     - a <code>tree:remainingItems</code> defining how many members can be reached when following this relation
 * a <code>tree:ViewDescription</code> is a subclass of <code>dcat:DataService</code> and serves a <code>tree:Collection</code>.
     - a <code>tree:search</code> describes a search form that allows an agent to jump to a specific <code>tree:Node</code>.

The first step when creating a TREE hypermedia interface is defining a collection of members:

<div class="example">
    ```turtle
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

From the moment this collection of members grows too large for one page, a fragmentation needs to be created in which an initial set of member can be found on an entry node, and more members can be found by interpreting the TREE hypermedia controls. This is illustrated by the next example:

<div class="example">
    ```turtle
    > HTTP GET https://example.org/Node1

    ex:Collection1 a tree:Collection;
                tree:view ex:Node1 ;
                tree:member ex:Subject1, ex:Subject2 .

    ex:Node1 a tree:Node ;
            tree:relation ex:R1,ex:R2 .

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
    1. As in the examples above: all quads with the object of the <code>tree:member</code> quads as a subject (and recursively the quads of their blank nodes) are by default included (see also [[!CBD]]), except when they would explicitely not be included in case 3, when the shape would be closed.
    2. Out of band / in band:
        - when no quads of a member have been found, the member will be dereferenced. This allows to publish the member on a separate page.
        - part of the member can be maintained elsewhere when a shape is defined (see 3)
    3. By defining a more complex shape with <code>tree:shape</code>, also nested entities can be included in the member
    4. By putting the triples in a named graph of the object of <code>tree:member</code>, all these triples will be matched. 
</div>

# Definitions # {#formalizations}

A <code>tree:Collection</code> is a set of <code>tree:Member</code>s. The set of members MAY be empty.

A <code>tree:Member</code> is a set of (at least one) quad(s) defined by the member extraction algorithm (next subsection).

A <code>tree:Node</code> is a dereferenceable resource containing <code>tree:Relation</code>s and a subset of (<code>⊆</code>) members of the collection. In a <code>tree:Node</code>, both the set of <code>tree:Relation</code>s as the subset of members MAY be empty. The same member MAY be contained in multiple nodes.

A <code>tree:Relation</code> is a function denoting a conditional link to another <code>tree:Node</code>.

A <code>tree:Node</code>, apart from the root node, has exactly one other <code>tree:Node</code> linking into it through one or more relations.

Note: The condition of multiple <code>tree:Relation</code>s to the same <code>tree:Node</code> MUST be combined with a logical AND.

A View is a specific set of interlinked <code>tree:Node</code>s, that together contain all members in a collection. A specific view will adhere to a certain growth or tree balancing strategy. In one View, completeness MUST be guaranteed, unless the View has a retention policy which becomes possible in LDES.

A <code>tree:search</code> form is an IRI template, that when filled out with the right parameters becomes a <code>tree:Node</code> IRI, or when dereferenced will redirect to a <code>tree:Node</code> from which all members in the collection that adhere to the described comparator can be found.

## The member extraction algorithm ## {#member-extraction-algorithm}

The set of quads the are part of the member, are defined by the [shape templates algorithm](https://w3id.org/tree/specification/shape-templates), provided as a separate report to this specification.
It is a combination of Concise Bounded Descriptions, named graphs and Shape Templates.
The latter uses the <code>sh:NodeShape</code> from the <code>tree:shape</code> property on the collections as an indication of the topology of the member graph.

Note: The way we process SHACL shapes into Shape Template is important to understand in order to know when an HTTP request will be triggered when designing SHACL shapes. A cardinality constraint not being exactly matched or a <code>sh:pattern</code> not being respected will not trigger an HTTP request, and instead just add the invalid quads to the Member. This is a design choice: we only define triggers for HTTP request from the SHACL shape to come to a complete set of quads describing the member the data publisher pointed at using <code>tree:member</code>.
