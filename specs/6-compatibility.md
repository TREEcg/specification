# Compatibility # {#compatibility}

## DCAT ## {#dcat}

[[!VOCAB-DCAT-2]] is the standard for Open Data Portals by W3C. In order to find TREE compliant datasets in data portals, there SHOULD be a <code>dcat:endpointDescription</code> from the <code>dcat:DataService</code> to the entrypoint where the <code>tree:Collection</code>s and the <code>tree:ViewDescription</code>s are listed. Furthermore, there SHOULD be a <code>dct:conformsTo</code> this URI: <code>https://w3id.org/tree</code>.

## Hydra ## {#hydra}

A <code>tree:Collection</code> is compatible with the [Hydra Collections specification](https://www.hydra-cg.com/spec/latest/core/#collections). However, instead of <code>hydra:view</code>, we use <code>tree:view</code> and do not link to a <code>hydra:PartialCollectionView</code> but to a <code>tree:Node</code>.
A <code>hydra:Collection</code> can thus also be extended with a <code>tree:shape</code> and <code>tree:view</code>.
When this is done, also <code>hydra:member</code> can be used instead of <code>tree:member</code>.

<code>hydra:totalItems</code> can be used to indicate the total amount of elements in the collection.
Hydra paging controls such as <code>hydra:next</code> and <code>hydra:previous</code> are semantically equivalent to a <code>tree:Relation</code> element that only contains a <code>tree:node</code> property.

## Activity Streams 2.0 ## {#activitystreams}

A <code>tree:Collection</code> is also compatible with [[!activitystreams-core]]’s specification of [paged collections](https://www.w3.org/TR/activitystreams-core/#collections).
Instead of <code>dcterms:isPartOf</code>, also <code>as:partOf</code> can be used to indicate that the current page is part of the full collection.
While Hydra and TREE link to the members of the collection by using the specific collection as a subject, Activity Streams 2.0 (AS) indicates a member starting from the page URL.
Therefore, when using AS collections, a client implementation should gather the members from the <code>tree:Node</code> or <code>as:CollectionPage</code> instead.

<code>as:totalItems</code> can be used to indicate the total amount of elements in the collection.

AS paging controls such as <code>as:next</code>and <code>as:previous</code> are semantically equivalent to a <code>tree:Relation</code> element that only contains a <code>tree:node</code> property.

## LDP Containers ## {#ldp}

In [[!LDP]], the <code>tree:view</code> can be applied on top of the <code>ldp:Container</code> instance.
Members can be found through <code>ldp:contains</code>, and/or through the indirect <code>ldp:membershipResource</code> and <code>ldp:hasMemberRelation</code> or <code>ldp:isMemberOfRelation</code> construct.

If this container is paged by the [[!ldp-paging]] (chapter 7) spec, then this MUST be ignored.

If there is an ordering, this MUST be ignored by TREE clients (the relations contain all necessary information for pruning).

## Shape trees ## {#shapetrees}

[The Shape Trees specification](https://shapetrees.org/TR/specification/) is specifically built to work within existing ecosystems.
As it was conceived to interoperate with LDP, the term Container in the Shape Trees spec can also be interpreted as a <code>tree:Collection</code>.
Shape Trees can help in the source selection of what specific <code>tree:Collection</code> to pick for your goal, and may add hierarchies to a set of <code>tree:Collection</code>s.
A client MAY infer a <code>tree:shape</code> of the collection through the <code>st:validatedBy</code> property of the Shapes Tree.

An example of a collection using Shape Tree terms. In this example a sensor with some observations is validated by using a [Shape Expressions](http://shex.io/shex-semantics/) (ShEx) file.

<div class="example">
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
</div>
