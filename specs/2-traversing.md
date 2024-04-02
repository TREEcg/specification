# The tree:Relations # {#relations}

The initial configuration of the <code>tree:Collection</code> and the description of the view is always provided when the view has been discovered, either in a separate document describing the view, either in the entry node itself. The configuration MUST be reused on any subsequent <code>tree:Node</code>.

## Traversing relations ## {#traversing}

A <code>tree:Node</code> element MAY have one or more <code>tree:relation</code> properties. A relation is an entity of the type <code>tree:Relation</code>, and MAY have a more specific type. A <code>tree:Relation</code> MUST have one <code>tree:node</code> object of the type <code>tree:Node</code>. By default, all nodes need to be followed, unless the client is able to select this relation for pruning (see next section).

The <code>tree:Relation</code>’s <code>tree:value</code> SHOULD be set. The object of <code>tree:value</code> SHOULD be accompanied by a data type when it is a literal value.

Every <code>tree:Relation</code> SHOULD have a <code>tree:path</code>, indicating the path from the member to the object on which the <code>tree:Relation</code> applies. For the different ways to express or handle a <code>tree:path</code>, we refer to [2.3.1 in the shacl specification](https://www.w3.org/TR/shacl/#x2.3.1-shacl-property-paths). All possible combinations of e.g., <code>shacl:alternativePath</code>, <code>shacl:inversePath</code> or <code>shacl:inLanguage</code> in the SHACL spec can be used. When <code>shacl:alternativePath</code> is used, the order in the list will define the importance of the order when evaluating the <code>tree:Relation</code>. A wildcard in the path is limited to the <code>tree:shape</code> of the <code>tree:Collection</code>.
The result of the evaluation of the <code>tree:path</code>, is the value that must be compared to the <code>tree:value</code>.

Every <code>tree:Relation</code> MAY provide a <code>tree:remainingItems</code>. A client MAY use <code>tree:remainingItems</code> to estimate the completeness of the downloaded elements to the end-user.

Note: When traversing, a client SHOULD keep a list of already visited pages, as despite this being the TREE spec, circular references and back-links are not explicitly prohibited.

A <code>tree:import</code> MAY be defined in the <code>tree:Relation</code> instance. When there is a <code>tree:path</code> defined, and when the relation is flagged interesting to follow, the import link needs to be downloaded in order to find the necessary literals to be compared (it is thus already a <code>tree:ConditionalImport</code>.

Note: An example of a <code>tree:import</code> is given [in the repository](https://github.com/TREEcg/specification/blob/master/examples/geospatially-ordered-public-transport/first.ttl#L27).

When dereferencing the object of a <code>tree:node</code> triple, the client MUST follow redirects. The URL to be used as the <code>tree:Node</code> URL is the last URL after redirects.

Note: This enables rebalancing search trees.

## Fallbacks ## {#fallbacks}

When there are no <code>tree:member</code>s and/or no <code>tree:Collection</code> defined, then still a <code>tree:Relation</code> can be defined. The <code>tree:path</code> in the <code>tree:Relation</code> then refers to a pattern that can start from every triple in the page.

When no <code>tree:path</code> is defined, the <code>tree:value</code> MUST be compared to all members’ triples that *can be compared to* the <code>tree:value</code> as defined by the type of the relation (or when no members or collection are defined, on every triple in the page).
When due to <code>rdfs:range</code> incompatibility, the object cannot be compared, the object will not be considered for comparison.

Note: This may enable server developers to indicate an index on all literals of the members (e.g., a prefix relation on title, description and body text) without having to indicate all of the alternative paths in the <code>tree:path</code>.

The target object of a <code>tree:path</code> SHOULD be materialized in the current Node document, but when it is not, the object MAY be considered implicit on the condition both <code>tree:path</code> and <code>tree:member</code> are defined.
In contrast to <code>shacl:path</code>, a <code>tree:path</code> MAY refer to an implicit property and may not be materialized in the current response. This may break SPARQL processors that did not yet come across the object before in their query plan. However, the tree may still be useful for query processors that, for example, prioritize queries according to the user’s location, and first download nodes that are nearby the user. Therefore, the materialized location of the object is not needed. While not recommended, possible heuristics could try to infer the data, could try to fetch it through another <code>tree:Collection</code>, or retrieve it using URI dereferencing.

## Specific relations ## {#relationsubclasses}

When the *only* type given for a certain Relation is <code>tree:Relation</code>, then the client must dereference all of the nodes. While this may seem useless, it can be used for the same use case as a <code>hydra:PartialCollectionView</code>.

For other types check the chapter on relation types in the vocabulary [](#Relation).

### Comparing strings ### {#strings}

String values have three specific type of relations: the <code>tree:PrefixRelation</code>, the <code>tree:SubstringRelation</code> and the <code>tree:SuffixRelation</code>.

Note: We experimented with server-chosen locales such that <code>ça suffit</code> can also be found when following a <code>tree:PrefixRelation</code> with a <code>tree:value "c"</code> (which at this moment is not supported). That would require an understanding of locales, and [browser/JavaScript support for locales is too low to be useful at this point](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation).

Also the comparator relations such as <code>tree:GreaterThanRelation</code> can be used.
The strings MUST then be compared according to *case sensitive unicode ordering*.

When a <code>tree:path</code> is defined, mind that you also may have to check the language of the element using the property <code>shacl:inLanguage</code> 
More languages MAY be set.
When no language is set, all strings are compared.

Note: If you want to have one resource containing both <code>e</code> and <code>é</code> as a prefix, you will have to create multiple relations to the same <code>tree:Node</code>.

### Comparing geospatial features ### {#geospatial}

The <code>tree:GeospatiallyContainsRelation</code> is the relation than can be used to express all further members will be contained within a geospatial region defined by the WKT String in the <code>tree:value</code>.

When using <code>tree:GeospatiallyContainsRelation</code>, the <code>tree:path</code> MUST refer to a literal containing a WKT string, such as <code>geosparql:asWKT</code>.

### Comparing time literals ### {#time}

When using relations such as <code>tree:LessThanRelation</code> or <code>tree:GreaterThanRelation</code>, the time literals need to be compared according to these 3 possible data types: <code>xsd:date</code>, <code>xsd:dateTime</code> or <code>xsd:dateTimeStamp</code>.

