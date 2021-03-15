# Core Concepts # {#core-concepts}

The root node from which all members of a collection can be discovered through the predicate `tree:view`.
The object of the `tree:view` triple will contain a root node from which all elements in the collection can be retrieved.

When the current page is a `tree:Node`, there MUST be a property linking the current page URL to the URI of the `tree:Collection`. Three properties may be used:
 1. `<collection> tree:view <currentPageUrl> .`<br/>May be used *only* in the case when the entire `tree:Collection` can be found starting from the current node.
 2. `<collection> void:subset <currentPageUrl> .`<br/>When the node is not a root node, but still a subset of the collection.
 3. `<currentPageUrl> dcterms:isPartOf <collection> .`<br/>The reverse property of 2.

## Multiple Collections ## {#multiple-collections}

When multiple collections are found by a client, it can choose to prune the collections based on the `tree:shape` property.
Therefore a data publisher SHOULD annotate a `tree:Collection` instance with a SHACL shape.
The `tree:shape` points to a SHACL description of the shape (`sh:NodeShape`).

Note: the shape can be a blank node, or a named node on which you should follow your nose when it is defined at a different HTTP URL.

## Multiple views ## {#multiple-views}

Multiple views MAY be provided, and a TREE client MUST traverse all objects of `tree:view` linked to this particular collection.
Every entity linked from `tree:view` MUST be an entry point to retrieve **all** members of the collection.

Note: How a client picks the right view is use-case specific. In order to fetch all members, one can be chosen at random. In order to prioritize a specific view, the relations and search forms in the root nodes can be studied for their relation types, path or remaining items.

## Compatibility ## {#compatibility}

### DCAT ### {#dcat}

[[!VOCAB-DCAT-2]] is the standard for Open Data Portals by W3C. In order to find TREE compliant datasets in data portals, there SHOULD be a `dcat:accessURL` from the `dcat:Distribution` to the entrypoint where the `tree:Collection`s are described. Furthermore, there SHOULD be a `dct:conformsTo` this URI: `https://w3id.org/tree`.

### Hydra ### {#hydra}

A `tree:Collection` is compatible with the [Hydra Collections specification](https://www.hydra-cg.com/spec/latest/core/#collections). However, instead of `hydra:view`, we use `tree:view` and do not link to a `hydra:PartialCollectionView` but to a `tree:Node`.
A `hydra:Collection` can thus also be extended with a `tree:shape` and `tree:view`.
When this is done, also `hydra:member` can be used instead of `tree:member`.

`hydra:totalItems` can be used to indicate the total amount of elements in the collection.

Hydra paging controls such as `hydra:next`and `hydra:previous`are semantically equivalent to a `tree:Relation` element that only contains a `tree:node` property. These do not provide any additional information to a client traversing a collection; the discovered node retains the value constraints from the current node. 

### Activity Streams 2.0 ### {#activitystreams}

A `tree:Collection` is also compatible with [[!activitystreams-core]]’s specification of [paged collections](https://www.w3.org/TR/activitystreams-core/#collections).
Instead of `dcterms:isPartOf`, also `as:partOf` can be used to indicate that the current page is part of the full collection.
While Hydra and TREE link to the members of the collection by using the specific collection as a subject, Activity Streams 2.0 (AS) indicates a member starting from the page URL.
Therefore, when using AS collections, a client implementation should gather the members from the `tree:Node` or `as:CollectionPage` instead.

`as:totalItems` can be used to indicate the total amount of elements in the collection.

AS paging controls such as `as:next`and `as:previous` are semantically equivalent to a `tree:Relation` element that only contains a `tree:node` property. These do not add provide any additional information to a client traversing a collection; the discovered node retains the value constraints from the current node. 

### LDP Containers ### {#ldp}

Another relevant spec is [[!LDP]].
There, the `tree:view` can be applied on top of the `ldp:Container` instance.
Members can be found through `ldp:contains`, and/or through the indirect `ldp:membershipResource` and `ldp:hasMemberRelation` or `ldp:isMemberOfRelation` construct.

If this container is paged by the [[!ldp-paging]] (chapter 7) spec, then this MUST be ignored.

If there is an ordering, this MUST be ignored by TREE clients (the relations contain all necessary information for pruning).

### Shape trees ### {#shapetrees}

[The Shape Trees specification](https://shapetrees.github.io/specification/spec) is specifically built to work within existing ecosystems.
As it was conceived to interoperate with LDP, the term Container in the Shape Trees spec can also be interpreted as a `tree:Collection`.
Shape Trees can help in the source selection of what specific `tree:Collection` to pick for your goal, and may add hierarchies to a set of `tree:Collection`s.
A client MAY infer a `tree:shape` of the collection through the `st:validatedBy` property of the Shapes Tree.

An example of a collection using Shape Tree terms. In this example a sensor with some observations is validated by using a [Shape Expressions](http://shex.io/shex-semantics/) (ShEx) file.

```turtle
@prefix sosa: <http://www.w3.org/ns/sosa/> .
@prefix om: <http://www.ontology-of-units-of-measure.org/resource/om-2/> .
@prefix ldp: <http://www.w3.org/ns/ldp#> .

<2021.ttl#Collection> a ldp:Container; 
    st:validatedBy <Sensor.shex#Sensor>;
    tree:member <sensor1>, <sensor2> .

<sensor1>
    a sosa:Sensor;
    sosa:madeObservation
        <sensor1-observation1>,
        <sensor1-observation2>;
    sosa:observes om:Temperature .

<sensor1-observation1>
    a sosa:Observation;
    sosa:observedProperty om:Temperature;
    sosa:madeBySensor <sensor1>;
    sosa:hasResult <result1>;
    sosa:resultTime "2020-08-25T07:05:31Z"^^xsd:dateTime .

<result1> a om:Measure; 
    om:hasValue "22"^^xsd:float; 
    om:hasUnit om:degreeCelsius .

<sensor1-observation2>
    a sosa:Observation;
    sosa:observedProperty om:Temperature;
    sosa:madeBySensor <sensor1>;
    sosa:hasResult <result2>;
    sosa:resultTime "2020-08-25T07:05:32Z"^^xsd:dateTime .

<result2> a om:Measure; 
    om:hasValue "22"^^xsd:float; 
    om:hasUnit om:degreeCelsius .

<sensor2>
    a sosa:Sensor;
    sosa:observes om:Temperature .
```

And its corresponding ShEx file (called Sensor.shex)

```shex
PREFIX sosa: <http://www.w3.org/ns/sosa/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX om: <http://www.ontology-of-units-of-measure.org/resource/om-2/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

<#Sensor> {
    a [sosa:Sensor] ;
    sosa:observes [om:Temperature]  ; 
    sosa:madeObservation @<#TemperatureObservation> * 
}

<#TemperatureObservation> {
    a [sosa:Observation] ;
    sosa:resultTime xsd:dateTime ;
    sosa:madeBySensor @<#Sensor> ? ;
    sosa:observedProperty [om:Temperature];
    sosa:hasResult @<#TemperatureResult> 
}

<#TemperatureResult> { 
    a [om:Measure];
    om:hasValue xsd:float ;
    om:hasUnit [om:degreeCelsius]
}
```
