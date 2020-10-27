# Extracting metadata # {#metadata}

The root node from which all members of a collection can be found can be discovered through the predicate `tree:view`.
The object of the `tree:view` triple will contain a root node from which all elements in the collection can be retrieved.

When the current page is a `tree:Node`, there MUST be a property linking the current page URL to the URI of the `tree:Collection`. Three properties may be used:
 1. `<collection> tree:view <currentPageUrl> .`<br/>May be used *only* in the case when the entire `tree:Collection` can be found starting from the current node.
 2. `<collection> void:subset <currentPageUrl> .`<br/>When the node is not a root node, but still a subset of the collection.
 3. `<currentPageUrl> dcterms:isPartOf <collection> .`<br/>The reverse property of 2.

## Multiple Collections ## {#multiple-collections}

When multiple collections are found by a client, it can choose to prune the collections to further traverse based on the `tree:shape` property.

## Multiple views ## {#multiple-views}

Multiple views MAY be provided, and a TREE client MUST traverse all objects of `tree:view` linked to this particular collection.
Every entity linked from `tree:view` MUST be an entry point to retrieve **all** members of the collection.

 that you need to iterate through all of the views, and pick one. The one you pick can be based on a priority list indicating how well it suits your purpose.

## Compatibility ## {#compatibility}

### DCAT ### {#dcat}

[[!VOCAB-DCAT-2]] is the standard for Open Data Portals by W3C. In order to find TREE compliant datasets in data portals, there SHOULD be a `dcat:accessURL` from the `dcat:Distribution` to the entrypoint where the `tree:Collection`s are described. Furthermore, there SHOULD be a `dct:conformsTo` this URI: `https://w3id.org/tree`.

### Hydra ### {#hydra}

A `tree:Collection` is compatible with the [Hydra Collections specification](https://www.hydra-cg.com/spec/latest/core/#collections). However, instead of `hydra:view`, we use `tree:view` and do not link to a `hydra:PartialCollectionView` but to a `tree:Node`.
A `hydra:Collection` can thus also be extended with a `tree:shape` and `tree:view`.
When this is done, also `hydra:member` can be used instead of `tree:member`.

`hydra:totalItems` can be used to indicate the total amount of elements in the collection.

Hydra paging controls such as first, last, next and previous MUST be ignored.

### Activity Streams 2.0 ### {#activitystreams}

A `tree:Collection` is also compatible with [[!activitystreams-core]]’s specification of [paged collections](https://www.w3.org/TR/activitystreams-core/#collections).
Instead of `dcterms:isPartOf`, also `as:partOf` can be used to indicate that the current page is part of the full collection.
While Hydra and TREE link to the members of the collection by using the specific collection as a subject, Activity Streams 2.0 indicates a member starting from the page URL.
Therefore, when using Activity Streams 2.0 collections, a client implementation should gather the members from the `tree:Node` or `as:CollectionPage` instead.

`as:totalItems` can be used to indicate the total amount of elements in the collection.

AS paging controls such as first, last, next and previous MUST be ignored.


### LDP Containers ### {#ldp}

Another relevant spec is [[!LDP]].
There, the `tree:view` can be applied on top of the `ldp:Container` instance.
Members can be found through `ldp:contains`, and/or through the indirect `ldp:membershipResource` and `ldp:hasMemberRelation` or `ldp:isMemberOfRelation` construct.

If this container is paged by the [[!ldp-paging]] (chapter 7) spec, then this MUST be ignored.

If there is an ordering, this MUST be ignored by TREE clients (the relations contain all necessary information for pruning).

### Shape trees ### {#shapetrees}

[The Shape Trees specification](https://shapetrees.github.io/specification/spec) is specifically built to work within existing ecosystems.
As it was conceived to interoperate with LDP, the term Container in the Shape Trees spec can also be interpreted as a `tree:Collection`.
Shape Trees can help in the source selection of what specific `tree:Collection` to pick for your goal, and may add hiearchies to a set of `tree:Collection`s.
A client MAY infer a `tree:shape` of the collection through the `st:validatedBy` property of the Shapes Tree.
