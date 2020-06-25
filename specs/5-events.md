# Event Streams

Event streams in TREE are defined as a `tree:Collection` of `tree:Event` members. Each `tree:Event` has three properties:
 - `prov:generatedAtTime` indicates the time at which the event was created.
 - `dcterms:subject` links to the instance of the object that was created at this moment (e.g., a _specific version of the representation_ of an object or a sensor observation).
 - `tree:shape` links to a SHACL shape description to which the `dcterms:subject` complies.

__Informative note 1:__ Chose `prov:generatedAtTime` over `dcterms:created` because the former has a stricter range (`xsd:dateTime`).

__Informative note 2:__ When implementing an event stream on top of a data model that does not have the concept of things that live in time, you will have to extend that model. One can solve that by using the concept of versions (using the predicate `dcterms:isVersionOf` in dcterms as in the following example:

```turtle
<C> a tree:Collection ;
    tree:member <E1> .

<E1> a tree:Event ;
    prov:generatedAtTime "2020..." ;
    tree:shape <shape1.shacl> ;
    dcterms:subject <V1> .

<V1> dcterms:isVersionOf <AddressRecord1> ;
    dcterms:title "Streetname X, ZIP Municipality, Country" .
```

A SPARQL query then has to query for version of this object as a way to give time-context to the query:

```sparql
SELECT * WHERE {
    ?event dcterms:subject ?address ;
           prov:generatedAtTime ?created .
    ?address dcterms:isVersionOf <AddressRecord1> ;
             dcterms:title ?addessString .
} ORDER BY DESC ?created LIMIT 1
```

A CONSTRUCT query can thus materialize the last version as follows:

```sparql
CONSTRUCT {
  <AddressRecord1> dcterms:title ?addressString .
} WHERE {
    ?event dcterms:subject ?address ;
           prov:generatedAtTime ?created .
    ?address dcterms:isVersionOf <AddressRecord1> ;
             dcterms:title ?addressString .
} ORDER BY DESC ?created LIMIT 1
```
