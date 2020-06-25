# Example: Event Streams

Event streams in TREE are defined as a `tree:Collection` of members with event-like objects. Each of such member has a timestamp (preferably with range `xsd:dateTime`) such as a `prov:generatedAtTime` indicating the time at which the event was created.
On each collection, a `tree:shape` __should__ be set that links to a SHACL shape to which the events comply.

Example:

```turtle
<C> a tree:Collection ;
    tree:shape <shape1.shacl> ; # this shacl shape for as long as this collection exists will need to be backwards compatible.
    tree:member <Obervation1> .

<Observation1> a tree:Event, sosa:Observation ;
    sosa:resultTime "2020..." ;
    sosa:hasSimpleResult "1" .
```

When implementing an event stream on top of a data model that does not have the concept of things that live in time, you will have to extend that model. One can solve that by using the concept of versions (using the predicate `dcterms:isVersionOf` in dcterms as in the following example:

```turtle
<C> a tree:Collection ;
    tree:shape <shape1.shacl> ; 
    tree:member <E1> .

<E1> a tree:Event ;
    prov:generatedAtTime "2020..." ;
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


