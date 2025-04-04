## Tree:relation in term of SPARQL algebra

An entity that describes a relation between two tree:Nodes.

The `tree:Relation` has specific sub-classes that implement a more specific type between the values. These types are described in the ontology (all classes are `rdf:subClassOf` `tree:Relation`). `tree:Relation` can be express in term of SPARQL algebra functions.

- String, Date or Number comparison:
    - `tree:PrefixRelation` — All elements in the related node have this prefix. Must conform to the [STRSTARTS](https://www.w3.org/TR/sparql11-query/#func-strstarts) SPARQL function.
    - `tree:SubstringRelation` —  All elements in the related node have this substring. Must conform to the [SUBSTR](https://www.w3.org/TR/sparql11-query/#func-substr) SPARQL function.
    - `tree:SuffixRelation` — All members of this related node end with this suffix. Must conform to the [STRENDS](https://www.w3.org/TR/sparql11-query/#func-strends) SPARQL function.
    - `tree:GreaterThanRelation` — the related Node’s members are greater than the value. Must conform to the SPARQL [Operator Mapping](https://www.w3.org/TR/sparql11-query/#OperatorMapping).
    - `tree:GreaterThanOrEqualToRelation`, `tree:LessThanRelation`, `tree:LessThanOrEqualToRelation`, `tree:EqualToRelation`, `tree:NotEqualToRelation`  — similar to ↑
    

- Geo-spatial comparison (requires the node values to be WKT-strings):
    - `tree:GeospatiallyContainsRelation` — Must conform to [geof:sfContains](https://docs.ogc.org/is/22-047r1/22-047r1.html#_b199579b-ac9d-4a2c-9df8-82325eba9660)