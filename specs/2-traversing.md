# Traversing Node elements # {#traversing}

A `tree:Node` element MAY have one or more `tree:relation` properties. A relation is an entity of the type `tree:Relation`, and MAY have a more specific type. A `tree:Relation` MUST have one `tree:node` object of the type `tree:Node`. By default, all nodes need to be followed, unless the client is able to select this relation for pruning (see next section).

The `tree:Relation`’s `tree:value` MUST be set. The object of `tree:value` SHOULD be accompanied by a data type when it is a literal value.

Every `tree:Relation` SHOULD have a `tree:path`, indicating on what triple(s) the `tree:value` applies. For the different ways to express or handle a `tree:path`, we refer to [2.3.1 in the shacl specification](https://www.w3.org/TR/shacl/#x2.3.1-shacl-property-paths). All possible combinations of e.g., `shacl:alternativePath`, `shacl:inversePath` or `shacl:inLanguage` in the SHACL spec can be used. When `shacl:alternativePath` is used, the order in the list will define the importance of the order when evaluating the `tree:Relation`.

When no `tree:path` is provided in this document, the `tree:value` must be compared to all objects that *can be compared to* the `tree:value` as defined by the type of the relation. 

Note: The latter enables server developers to indicate an index on all literals of the members (e.g., a prefix relation on title, description and body text) without having to indicate all of the alternative paths in the `tree:path`.

When a `tree:path` is defined, and
 1. When no `tree:member` relations are defined, the `tree:path` needs to be evaluated on all triples in the page
 2. When a `tree:member` relationship is defined, the `tree:path` starts from the member URI. The target object SHOULD be materialized in the current Node document, but when it is not, the object MAY be considered implicit (only on the condition both `tree:path` and `tree:member` is defined).

The result of the evaluation of the `tree:path`, is the value that must be compared to the `tree:value`.
When no `tree:path` is defined, the `tree:value` applies to all comparable properties of the `tree:member` elements.
When also no `tree:member` is defined, the `tree:value` MUST be compared to all triples in the document.
When due to `rdfs:range` incompatibility, the object cannot be compared, the object will not be considered for comparison.

Every node MAY provide a `tree:remainingItems`. A client MAY use `tree:remainingItems` to estimate the completeness of the downloaded elements to the end-user.

Note: Not having a `tree:member` nor `tree:path` may also be useful for triple-based indexes such as [Triple Pattern Fragments](https://www.hydra-cg.com/spec/latest/triple-pattern-fragments/). In order to support metadata about the triples itself, something like [RDF*](http://blog.liu.se/olafhartig/tag/rdf-star/) would otherwise be needed, or a triple indicating whether we should look at the page as a “page of triples” or a “page of members”.

Note: A client needs to keep a list of already visited pages, as despite this being the TREE spec, circular references and back-links are not explicitly prohibited.

A `tree:import`MAY be defined in the `tree:Relation` instance. When there is a `tree:path` defined, and when the relation is flagged interesting to follow, the import link needs to be downloaded in order to find the necessary literals to be compared.

Note: In contrast to `shacl:path`, a `tree:path` MAY refer to an implicit property and may not materialized in the current response. This may break SPARQL processors that did not yet come across the object before in their query plan. However, the tree may still be useful for query processors that, for example, prioritize queries according to the user’s location, and first download nodes that are nearby the user. Therefore, the materialized location of the object is not needed. While not recommended, possible heuristics could try to inferred the data, could try to fetch it through another `tree:Collection`, or retrieve it using URI dereferencing. An example of a `tree:import` is given here: https://github.com/TREEcg/specification/blob/master/examples/geospatially-ordered-public-transport/first.ttl#L27.

Instead of a `tree:value`, also a `tree:qualifiedValue` MAY be used. This is a qualification of a `tree:value` and contains a way to download and reuse an existing fragmentation.

Note: an example of how to use a qualified value can be viewed here: https://github.com/TREEcg/specification/tree/master/examples/qualified-values.

## Specific relations ## {#relations}

When the *only* type given for a certain Relation is `tree:Relation`, then the client must dereference all of the nodes. While this may seem useless, it can be used for the same use case as a `hydra:PartialCollectionView`.

For other types: see [vocabulary](../vocabulary.md) for now.

### Comparing strings ### {#strings}

When using the `tree:PrefixRelation` or the `tree:SubstringRelation`, the strings MUST be compared according to *case insensitive unicode ordering*.
Some flags MAY however indicate a small derivation from this approach:

Issue: The following text has no good JavaScript support and is indicative.

A comparison based on locale and other options can be done by using these predicates:
 1. `tree:stringComparisonLocale`: a BCP 47 language as defined in JavaScript: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation
 2. `tree:stringComparisonLocaleMatcher`
 3. `tree:stringComparisonUsage`
 4. `tree:stringComparisonSensitivity`
 5. `tree:stringComparisonIgnorePunctuation`
 6. `tree:stringComparisonNumeric`
 7. `tree:stringComparisonCaseFirst`

When a `tree:path` is defined, mind that you also may have to check the language of the element using the property `shacl:inLanguage` 
More languages MAY be set.
When no language is set, all strings are compared.

Note: By default this thus means, when typing `à`, the links to `a` can be pruned.

Note: The settings used for autocompletion on the client may be different on the client than on the server. The only thing the string comparison settings are used for, is for the client to understand whether it can safely prune its search tree or not.

### Comparing geospatial features ### {#geospatial}

The `tree:GeospatiallyContainsRelation` is the relation than can be used to express all further members will be contained within a geospatial region defined by the WKT String in the `tree:value`.

The `tree:path` MUST refer to a literal containing a WKT string, such as `geosparql:asWKT`.

### Comparing time literals ### {#time}

When using relations such as `tree:LessThanRelation` or `tree:GreaterThanRelation`, the time literals need to be compared according to these 3 possible data types: `xsd:date`, `xsd:dateTime` or `xsd:dateTimeStamp`.
<!--
## Compliance testing ## {#compliance-traversing}

You can test compliance if the following query executed on your page gives a valid response. You need the current page URL and bind this to `<page_url>`.

Finding relations:

```sparql
SELECT ?relationType ?value ?node
WHERE {
  ?collection void:subset|tree:view|^dcterms:isPartOf <page_url> ;
              tree:relation ?relation .
  ?relation a ?relationType ;
            tree:value ?value ;
            tree:node ?node .
}
```

Finding all elements in the current page:

```sparql
CONSTRUCT {
  ?s ?p ?o .
} WHERE {
  ?collection void:subset|tree:view|^dcterms:isPartOf <page_url> ;
              tree:member ?s .
  ?s ?p ?o .
}
```
-->
