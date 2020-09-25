# Example: Event Streams

Event streams in TREE are defined as a `tree:Collection` of members with event-like objects. Each of such member has a timestamp (preferably with range `xsd:dateTime`) such as a `prov:generatedAtTime` indicating the time at which the event was created.
On each collection, a `tree:shape` __should__ be set that links to a SHACL shape to which the events comply.

Example:

```turtle
<C> a tree:Collection ;
    tree:shape <shape1.shacl> ; # this shacl shape for as long as this collection exists will need to be backwards compatible.
    tree:member <Obervation1> .

<Observation1> a sosa:Observation ;
    sosa:resultTime "2020..." ;
    sosa:hasSimpleResult "1" .
```

When implementing an event stream on top of a data model that does not have the concept of things that live in time, you will have to extend that model. One can solve that by using the concept of versions (using the predicate `dcterms:isVersionOf` in dcterms as in the following example:

```turtle
<C> a tree:Collection ;
    tree:shape <shape1.shacl> ; 
    tree:member <E1> .

<E1> prov:generatedAtTime "2020..." ;
     dcterms:isVersionOf <AddressRecord1> ;
     dcterms:title "Streetname X, ZIP Municipality, Country" .
```

A SPARQL query then has to query for version of this object as a way to give time-context to the query:

```sparql
SELECT * WHERE {
    ?address dcterms:isVersionOf <AddressRecord1> ;
             prov:generatedAtTime ?created ;
             dcterms:title ?addressString .
} ORDER BY DESC ?created LIMIT 1
```

A CONSTRUCT query can thus materialize the last version as follows:

```sparql
CONSTRUCT {
  <AddressRecord1> dcterms:title ?addressString .
} WHERE {
    ?address dcterms:isVersionOf <AddressRecord1> ;
             prov:generatedAtTime ?created ;
             dcterms:title ?addressString .
} ORDER BY DESC ?created LIMIT 1
```

## Fragmentation strategies

The main interesting fragmentation strategy for an event stream is to split it in time.
This can be done with simple TREE relations such as: `tree:GreaterThanOrEqualRelation` and `tree:LessThanOrEqualRelation`.

The first page of the event stream starts with the oldest events:

```turtle
<C> a tree:Collection ;
    tree:shape <shape1.shacl> ; # this shacl shape for as long as this collection exists will need to be backwards compatible.
    tree:member <Obervation1>, ... ;
    tree:view <?page=1> .

<?page=1> a tree:Node ;
    tree:relation [
        a tree:GreaterThanOrEqualRelation ;
        tree:path sosa:resultTime ;
        tree:node <?page=2> ;
        tree:value "2020-12-24T12:00:00Z"^^xsd:dateTime
    ] .
```

Also other links can be added to later pages.

A `tree:importStream` can be used on the last page to import events through pubsub as they happen in time.

## Hydra search forms

Using a `hydra:search` form with `hydra:property` `tree:timeQuery` you may add a search form to directly address a certain page containing events in a time interval.

