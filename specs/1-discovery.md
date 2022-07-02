# Core Concepts # {#core-concepts}

A node from which all members of a collection can be discovered, can be found through a triple stating `ex:C1 tree:view ex:N1` with `ex:C1` being a `tree:Collection` and `ex:N1` being a `tree:Node`.

When the current page is a `tree:Node`, there MUST be a property linking the current page URL to the URI of the `tree:Collection`. However, not from all `tree:Node`s all members can be reached, and therefore 2 other properties can be used: `void:subset` and `dcterms:isPartOf`.

Three properties may thus be used:
 1. `ex:C1 tree:view <> .`<br/>May be used *only* in the case when the entire `tree:Collection` can be found starting from the current node.
 2. `ex:C1 void:subset <> .`<br/>When the node is not a node from which all members can be found, but still a subset of the collection can be found.
 3. `<> dcterms:isPartOf ex:C1 .`<br/>The reverse property of 2.

## Selecting from multiple Collections ## {#multiple-collections}

When multiple collections are found by a client, it can choose to prune the collections based on the `tree:shape` property.
Therefore a data publisher SHOULD annotate a `tree:Collection` instance with a SHACL shape.
The `tree:shape` points to a SHACL description of the shape (`sh:NodeShape`).

Note: the shape can be a blank node, or a named node on which you should follow your nose when it is defined at a different HTTP URL.

Note: For compatibility with the [Solid specifications](https://solidproject.org/TR/), a ShEx shape may also be given (see the chapter on compatibility bellow).

## Selecting a view from multiple views ## {#multiple-views}

Every entity linked from `tree:view` MUST be an entry point to retrieve **all** members of the collection.
Multiple `tree:view` links MAY be provided, and a TREE client MUST traverse all relations from the `tree:Node`s linked to this particular collection.

Note: A `tree:Node` linked through `tree:view` can thus be used to _view_ all members of the collection, hence the name (this is similar in the Hydra specification).  

How a client picks the right view is use-case specific, and can be prioritized by studying the `tree:ViewDescription`’s properties (see next subsection).
In order to fetch all members, one can be chosen at random if no specific `tree:ViewDescription` is given.
In order to prioritize a specific view, the relations and search forms in the root nodes can be studied for their relation types, path or remaining items.


### Describing the ViewDescription ### {#view-description}

The class `tree:ViewDescription` indicates a specific TREE structure on a `tree:Collection`.
It is an `rdfs:subClassOf` a `dcat:DataService`.
Through the property `tree:viewDescription` 
```
ex:N1 a tree:Node ;
  tree:viewDescription ex:Fragmentation1 .
  
ex:Fragmentation1 a tree:ViewDescription ; #this is an rdfs:subClassOf dcat:DataService
  dcat:endpointURL ex:N1 ; # The entry point that can be advertised in a data portal
  dcat:servesDataset ex:C1 .
```

Note: In Linked Data Event Streams, the [`ldes:EventSource` class](https://w3id.org/ldes#EventSource) exists to indicate this fragmentation is designed to be the source for all derived views. The Linked Data Evnet Streams specification can also further elaborate on the ViewDescription by for example describing a retention policy on top of it.

Note: In [the Smart Data Specification](https://w3id.org/sds/specification), a `tree:ViewDescription` can be used to describe the algorithm that created this specific fragmentation.

A `tree:Node` can also be double typed as the `tree:ViewDescription`. A client must thus check for ViewDescriptions on both the current node without the `tree:viewDescription` qualification, as on the current node with the `tree:viewDescription` link.
