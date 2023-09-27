# Discovery and source selection# {#hypermedia}

TREE tackles discovery on three levels: i) interface discovery, ii) view discovery, and iii) dataset discovery.
Interface discovery discovers what collection the current page is part of, and discovers what the next possible HTTP requests are through relations and search forms.
One dataset can have multiple views that can be published across different servers, selecting one for a certain use case is part of the view discovery.
Dataset discovery is then selecting a `tree:Collection` of interest.


## Interface discovery ## {#interface-discovery}

Interface discovery starts when a URL is provided to a specific `tree:Node`.
A node from which all members of a collection can be discovered (an “entry node”), can be found through a triple stating `ex:C1 tree:view ex:N1` with `ex:C1` being a `tree:Collection` and `ex:N1` being a `tree:Node`.

When the current page is a `tree:Node`, there MUST be a property linking the current page URL to the URI of the `tree:Collection`. However, not from all `tree:Node`s all members can be reached, and therefore 2 other properties can be used: `void:subset`, or the inverse property, `dcterms:isPartOf`.

Three properties MAY thus be used:
 1. `ex:C1 tree:view <> .`<br/>May be used *only* in the case when the entire `tree:Collection` can be found starting from the current node.
 2. `ex:C1 void:subset <> .`<br/>When the node is not a node from which all members can be found, but still is a subset of the collection that can be found.
 3. `<> dcterms:isPartOf ex:C1 .`<br/>The inverse property of 2.

We refer to next chapters for traversing across multiple relations, or for using search forms.

## View discovery ## {#multiple-views}

Every node linked from `tree:view` MUST be an entry point to retrieve **all** members of the collection.
Multiple `tree:view` links MAY be provided.
If a TREE client wants to guarantee compleneteness, it picks one link and then traverses all relations.

Note: How a client picks the right view is use case specific. The `tree:ViewDescription`’s properties can help in that regards.

In order to prioritize a specific view link, the relations and search forms in the entry nodes can be studied for their relation types, path or remaining items.
The class `tree:ViewDescription` indicates a specific TREE structure on a `tree:Collection`.
Through the property `tree:viewDescription` a `tree:Node` can link to an entity that describes the view, and can be reused in data portals as the `dcat:DataService`.

<div class="example">
    ```turtle
    ## What can be found in a tree:Node
    ex:N1 a tree:Node ;
      tree:viewDescription ex:View1 .
      
    ex:C1 a tree:Collection ;
      tree:view ex:N1 .

    ## What can be found on a data portal
    ex:C1 a dcat:Dataset .
    ex:View1 a tree:ViewDescription, dcat:DataService ;
      dcat:endpointURL ex:N1 ; # The entry point that can be advertised in a data portal
      dcat:servesDataset ex:C1 .
    ```
</div>

When there is no `tree:viewDescription` property in this page, a client either already discovered the description of this view in an earlier `tree:Node`, either the current `tree:Node` is implicitly the ViewDescription. Therefore, when the property path `tree:view → tree:viewDescription` does not yield a result, the view properties MUST be extracted from the object of the `tree:view` triple.
A `tree:Node` can also be double typed as the `tree:ViewDescription`. A client must thus check for ViewDescriptions on both the current node without the `tree:viewDescription` qualification, as on the current node with the `tree:viewDescription` link.


## Dataset discovery ## {#multiple-collections}

When multiple collections are found by a client, it can choose to prune the collections based on the `tree:shape` property.
Therefore a data publisher SHOULD annotate a `tree:Collection` instance with a SHACL shape.
The `tree:shape` points to a SHACL description of the shape (`sh:NodeShape`).

Note: the shape can be a blank node, or a named node on which you should follow your nose when it is defined at a different HTTP URL.

Note: For compatibility with the [Solid specifications](https://solidproject.org/TR/), a ShEx shape may also be given (see the chapter on compatibility bellow).
