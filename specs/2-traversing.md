# Relations # {#relations}

## Traversing relations ## {#traversing}

A `tree:Node` element MAY have one or more `tree:relation` properties. A relation is an entity of the type `tree:Relation`, and MAY have a more specific type. A `tree:Relation` MUST have one `tree:node` object of the type `tree:Node`. By default, all nodes need to be followed, unless the client is able to select this relation for pruning (see next section).

The `tree:Relation`’s `tree:value` SHOULD be set. The object of `tree:value` SHOULD be accompanied by a data type when it is a literal value.

Every `tree:Relation` SHOULD have a `tree:path`, indicating the path from the member to the object on which the `tree:Relation` applies. For the different ways to express or handle a `tree:path`, we refer to [2.3.1 in the shacl specification](https://www.w3.org/TR/shacl/#x2.3.1-shacl-property-paths). All possible combinations of e.g., `shacl:alternativePath`, `shacl:inversePath` or `shacl:inLanguage` in the SHACL spec can be used. When `shacl:alternativePath` is used, the order in the list will define the importance of the order when evaluating the `tree:Relation`. A wildcard in the path is limited to the `tree:shape` of the `tree:Collection`.
The result of the evaluation of the `tree:path`, is the value that must be compared to the `tree:value`.

Every `tree:Relation` MAY provide a `tree:remainingItems`. A client MAY use `tree:remainingItems` to estimate the completeness of the downloaded elements to the end-user.

Note: When traversing, a client SHOULD keep a list of already visited pages, as despite this being the TREE spec, circular references and back-links are not explicitly prohibited.

A `tree:import` MAY be defined in the `tree:Relation` instance. When there is a `tree:path` defined, and when the relation is flagged interesting to follow, the import link needs to be downloaded in order to find the necessary literals to be compared (it is thus already a `tree:ConditionalImport`.

Note: An example of a `tree:import` is given [in the repository](https://github.com/TREEcg/specification/blob/master/examples/geospatially-ordered-public-transport/first.ttl#L27).

## Fallbacks ## {#fallbacks}

When there are no `tree:member`s and/or no `tree:Collection` defined, then the `tree:path` refers to a pattern that can start from every triple in the page.

Note: This may enable server developers to indicate an index on all literals of the members (e.g., a prefix relation on title, description and body text) without having to indicate all of the alternative paths in the `tree:path`.

When no `tree:path` is defined, the `tree:value` MUST be compared to all members’ triples that *can be compared to* the `tree:value` as defined by the type of the relation (or when no members or collection are defined, on every triple in the page).
When due to `rdfs:range` incompatibility, the object cannot be compared, the object will not be considered for comparison.

When there is no `tree:shape` given, then this collection may contain any kind of triple and can not be automatically pruned in the source selection. This may however still be useful in triple-based collections.

Note: Not having a `tree:member` nor `tree:path` may also be useful for triple-based indexes such as [Triple Pattern Fragments](https://www.hydra-cg.com/spec/latest/triple-pattern-fragments/). In order to support metadata about the triples itself, something like [RDF*](http://blog.liu.se/olafhartig/tag/rdf-star/) would otherwise be needed, or a triple indicating whether we should look at the page as a “page of triples” or a “page of members”.

The target object SHOULD be materialized in the current Node document, but when it is not, the object MAY be considered implicit on the condition both `tree:path` and `tree:member` are defined.
In contrast to `shacl:path`, a `tree:path` MAY refer to an implicit property and may not materialized in the current response. This may break SPARQL processors that did not yet come across the object before in their query plan. However, the tree may still be useful for query processors that, for example, prioritize queries according to the user’s location, and first download nodes that are nearby the user. Therefore, the materialized location of the object is not needed. While not recommended, possible heuristics could try to infer the data, could try to fetch it through another `tree:Collection`, or retrieve it using URI dereferencing.

## Specific relations ## {#relationsubclasses}

When the *only* type given for a certain Relation is `tree:Relation`, then the client must dereference all of the nodes. While this may seem useless, it can be used for the same use case as a `hydra:PartialCollectionView`.

For other types check the chapter on relation types in the vocabulary [](#Relation).

### Comparing strings ### {#strings}

String values have three specific type of relations: the `tree:PrefixRelation`, the `tree:SubstringRelation` and the `tree:SuffixRelation`.

Note: We experimented with server-chosen locales such that `ça suffit` can also be found when following a `tree:PrefixRelation` with a `tree:value "c"` (which at this moment is not supported). That would require an understanding of locales, and [browser/JavaScript support for locales is too low to be useful at this point](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation).

Also the comparator relations such as `tree:GreaterThanRelation` can be used.
The strings MUST then be compared according to *case sensitive unicode ordering*.

When a `tree:path` is defined, mind that you also may have to check the language of the element using the property `shacl:inLanguage` 
More languages MAY be set.
When no language is set, all strings are compared.

Note: If you want to have one resource containing both `e` and `é` as a prefix, you will have to create multiple relations to the same `tree:Node`.

### Comparing geospatial features ### {#geospatial}

The `tree:GeospatiallyContainsRelation` is the relation than can be used to express all further members will be contained within a geospatial region defined by the WKT String in the `tree:value`.

When using `tree:GeospatiallyContainsRelation`, the `tree:path` MUST refer to a literal containing a WKT string, such as `geosparql:asWKT`.

### Comparing time literals ### {#time}

When using relations such as `tree:LessThanRelation` or `tree:GreaterThanRelation`, the time literals need to be compared according to these 3 possible data types: `xsd:date`, `xsd:dateTime` or `xsd:dateTimeStamp`.

