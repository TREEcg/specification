# Discovery and source selection# {#hypermedia}

TREE tackles discovery and source selection on three levels: i) interface discovery, ii) view discovery, and iii) dataset discovery.
Interface discovery discovers what collection the current page is part of, and discovers what the next possible HTTP requests are through relations and search forms.
One dataset can have multiple views that can be published across different servers, selecting one for a certain use case is part of the view discovery.
Dataset discovery is then selecting a <code>tree:Collection</code> of interest.


## Interface discovery ## {#interface-discovery}

Interface discovery starts when a URL is provided to a specific <code>tree:Node</code>.
A node from which all members of a collection can be discovered (an “entry node”), can be found through a triple stating <code>ex:C1 tree:view ex:N1</code> with <code>ex:C1</code> being a <code>tree:Collection</code> and <code>ex:N1</code> being a <code>tree:Node</code>.

When the current page is a <code>tree:Node</code>, there MUST be a property linking the current page URL to the URI of the <code>tree:Collection</code>. However, not from all <code>tree:Node</code>s all members can be reached, and therefore 2 other properties can be used: <code>void:subset</code>, or the inverse property, <code>dcterms:isPartOf</code>.

<code>ex:C1 tree:view <> .</code> links the current page to the <code>tree:Collection</code>.

We refer to next chapters for traversing across multiple relations, or for using search forms.

## View discovery ## {#multiple-views}

Todo: This will be reworked

Note: How a client picks the right view is use case specific. The <code>tree:ViewDescription</code>’s properties can help in that regards.

In order to prioritize a specific view link, the relations and search forms in the entry nodes can be studied for their relation types, path or remaining items.
The class <code>tree:ViewDescription</code> indicates a specific TREE structure on a <code>tree:Collection</code>.
Through the property <code>tree:viewDescription</code> a <code>tree:Node</code> can link to an entity that describes the view, and can be reused in data portals as the <code>dcat:DataService</code>.

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

When there is no <code>tree:viewDescription</code> property in this page, a client either already discovered the description of this view in an earlier <code>tree:Node</code>, either the current <code>tree:Node</code> is implicitly the ViewDescription. Therefore, when the property path <code>tree:view → tree:viewDescription</code> does not yield a result, the view properties MUST be extracted from the object of the <code>tree:view</code> triple.
A <code>tree:Node</code> can also be double typed as the <code>tree:ViewDescription</code>. A client must thus check for ViewDescriptions on both the current node without the <code>tree:viewDescription</code> qualification, as on the current node with the <code>tree:viewDescription</code> link.


## Dataset discovery ## {#multiple-collections}

When multiple collections are found by a client, it can choose to prune the collections based on the <code>tree:shape</code> property.
Therefore a data publisher SHOULD annotate a <code>tree:Collection</code> instance with a SHACL shape.
The <code>tree:shape</code> points to a SHACL description of the shape (<code>sh:NodeShape</code>).

Note: the shape can be a blank node, or a named node on which you should follow your nose when it is defined at a different HTTP URL.

Note: For compatibility with the [Solid specifications](https://solidproject.org/TR/), a ShEx shape may also be given (see the chapter on compatibility bellow).
