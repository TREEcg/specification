# Introduction # {#introduction}

A TREE hypermedia interface starts with defining a collection of members:

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

From the moment this collection of members grows too big for one page, a fragmentation needs to be created in which an initial set of member can be found on an entry point, and more members can be found by interpreting the TREE _hypermedia controls_.

## Overview ## {#overview}

<img src="https://docs.google.com/drawings/d/e/2PACX-1vTTCjBkBum1J4xgbg0oZJaD_H05dpZxhL6jrp1yzqoIsYw5EOa-7D24No_rfEyTipq1rLb-_tPTEYV0/pub?w=1093&amp;h=546" width="100%">

The TREE specification introduces these core concepts:
 * a `tree:Collection` is a subclass of `dcat:Dataset`. The specialization is that it is a collection of members that MAY adhere to a certain shape. It typically has these properties when described in a node:
     - `tree:member` indicates the object is a member of the collection.
     - `tree:view` indicates an entry node from which all members can be reached.
     - `tree:shape` indicates the SHACL [[!SHACL]] shape to which each member in the collection adheres.
     - `tree:viewDescription` links to a description of the view (a `tree:ViewDescription`). Multiple descriptions MAY be provided that MUST be combined.
 * a `tree:Node`: is a page on which relations to other pages are described through the `tree:relation` predicate, and/or through which a next `tree:Node` can be found by using the `tree:search` form.
 * a `tree:Relation` is a relation from one node to another. An extension of this class indicates a specific type of relation (e.g., a `tree:GreaterThanRelation`). A relation typically has these properties:
     - a `tree:node` the URL of the other node
     - a `tree:path` indicating to which of the members' properties this relation applies
     - a `tree:value` indicating a value constraint on the members' values
     - a `tree:remainingItems` defining how many members can be reached when following this relation
 * a `tree:ViewDescription` is a subclass of `dcat:DataService` and serves a `tree:Collection`.
     - a `tree:search` describes a search form that allows one to jump to a specific `tree:Node`.

<div class="example">
```
> HTTP GET https://example.org/Node1

ex:Collection1 a tree:Collection;
               tree:view ex:Node1 .
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

Different types of `tree:Collection`s exist. In this spec, there are two types that change the behaviour of a client:
 1. `tree:Collection` (default): uses `tree:member` to point at the primary topic of the `tree:Member`. The `tree:Member` itself is not identified in this collection.
 2. `tree:NamedGraphCollection`: extends `tree:Collection` by using `tree:member` to point at the named graph containing exclusively a complete set of triples of that member. The primary topic of the member is described on the IRI of the named graph using the `foaf:primaryTopic` property.

## Formally ## {#formalizations}

A `tree:Collection` is a set of `tree:Member`s. A `tree:Member` is a set of triples defined by the member extraction algorithm (next subsection).

A `tree:Node` is a set of `tree:Relation`s and a subset of (`⊆`) members of the collection. Each set MAY be empty. The same member MAY be contained in multiple nodes.

A `tree:Relation` is a function that is a conditional link to another `tree:Node`. The condition of multiple Relations MUST be combined with a logical AND.

A View is a specific set of interlinked `tree:Node`s, that together contain all members in a collection, and that adhere to a certain growth strategy. In one view, completeness MUST be guaranteed.

A `tree:search` is a IRI template, that when filled out with the right parameters becomes a `tree:Node` IRI, or when dereferenced will redirect to a `tree:Node` from which all members in the collection that adhere to the described comparator can be found.

## Member extraction algorithm ## {#member-extraction-algorithm}

Note: A client may be developed with the finality of selecting triples (e.g., finding an answer to a SPARQL query) or with the finality to show all triples of a member (e.g., for replicating/synchronizing data, or for autocompleting entities). Either way, with TREE, they are always downloading the full set of triples of a member.

__Step 1__: Store the objects of triples adhering to the pattern `<CollectionOfInterest> tree:member ?o` in M.

__Step 2__: Establish the kind of the collection: is this a `tree:Collection`, or is it a `tree:NamedGraphCollection`?

__Step 2.a__: If it is a `tree:NamedGraphCollection`, yield the members by extracting the `foaf:primaryTopic` triple of the graph, and extracting all triples in the graph.

__Step 2.b__: If it is a `tree:Collection` look up all triples with the member as its subject. Repeat the process with the named and blank nodes from the objects of these triples, but exclude other members from the collection and prevent cycles.

Step 2 should yield something of this form:

```
<G1> a tree:Member ; # can be implied if it’s a result from a TREE client
     foaf:primaryTopic <S1> .
GRAPH <G1> {
   <S1> a ex:Subject ; ... 
}
``` 

Note: The members of a `tree:Collection` without named graphs is thus limited in a couple of ways: triples must be joint through forward relations, and members cannot contain triples from other members.
