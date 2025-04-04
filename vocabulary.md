# Vocabulary # {#vocabulary}

**Namespace**: <code>https://w3id.org/tree#</code>

Prefixes:

```turtle
@prefix tree: <https://w3id.org/tree#>.
@prefix hydra: <http://www.w3.org/ns/hydra/core#>.
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
```

## Classes ## {#classes}

### tree:Collection ### {#collection}

A collection has members that MAY adhere to a certain shape.

### tree:Node ### {#Node}

A <code>tree:Node</code> is a node that MAY contain links to other dereferenceable resources that lead to a full overview of a <code>tree:Collection</code>.

### tree:Relation ### {#Relation}

An entity that describes a relation between two <code>tree:Nodes</code>.

The <code>tree:Relation</code> has specific sub-classes that implement a more specific type between the values.
These types are described in the ontology (all classes are <code>rdf:subClassOf</code> <code>tree:Relation</code>).
<code>tree:Relation</code>  can be express in term of[SPARQL algebra functions](https://www.w3.org/TR/sparql11-query/#expressions):
 - String, Date or Number comparison:
     - <code>tree:PrefixRelation</code> — All elements in the related node have this prefix.
        MUST conform to the [STRSTARTS](https://www.w3.org/TR/sparql11-query/#func-strstarts) SPARQL function.
     - <code>tree:SubstringRelation</code> — All elements in the related node have this substring.
        MUST conform to the [SUBSTR](https://www.w3.org/TR/sparql11-query/#func-substr) SPARQL function.
     - <code>tree:SuffixRelation</code> — All members of this related node end with this suffix.
        MUST conform to the [STRENDS](https://www.w3.org/TR/sparql11-query/#func-strends) SPARQL function.
     - <code>tree:GreaterThanRelation</code> — the related Node’s members are greater than the value.
        MUST conform to the SPARQL [Operator Mapping](https://www.w3.org/TR/sparql11-query/#OperatorMapping).
     - <code>tree:GreaterThanOrEqualToRelation</code> — similar to ↑
     - <code>tree:LessThanRelation</code>
     - <code>tree:LessThanOrEqualToRelation</code>
     - <code>tree:EqualToRelation</code>
     - <code>tree:NotEqualToRelation</code>
 - Geo-spatial comparison (requires the node values to be WKT-strings): 
     - <code>tree:GeospatiallyContainsRelation</code> — MUST conform to [geof:sfContains](https://docs.ogc.org/is/22-047r1/22-047r1.html#_simple_features_relation_family).

For more detailed behavior related to specific types, see the section [Traversing the search tree](#traversing).

### tree:ConditionalImport ### {#ConditionalImport}

A class to import a file or a stream based on a <code>tree:path</code> of properties. This way it can import the necessary data for complying to the SHACL shape, or evaluating a relation type.

## Properties ## {#properties}

### tree:relation ### {#relation}

Links a node to a relation

**Domain**: <code>tree:Node</code>

**Range**: <code>tree:Relation</code>


### tree:remainingItems ### {#remainingItems}

Remaining number of items of this node, the items in its children included.

**Domain**: <code>tree:Relation</code>

**Range**: <code>xsd:integer</code>

### tree:node ### {#node}

The URL to be derefenced when this relation cannot be pruned.

**Domain**: <code>tree:Relation</code>

**Range**: <code>tree:Node</code>

### tree:value ### {#value}

The contextual value of this node: may contain e.g., a WKT-string with the bound of a rectangle, may contain a string, an integer, or even link to another resource where clear comparison rules apply.

**Domain**: <code>tree:Relation</code>

### tree:path ### {#path}

A property path, [as defined by SHACL](https://www.w3.org/TR/shacl/#x2.3.1-shacl-property-paths), that indicates what resource the <code>tree:value</code> affects.

See [](#relations)

**Domain**: <code>tree:Relation</code>

### tree:view ### {#view}

Links the collection to the current <code>tree:Node</code>.

**Domain**: <code>tree:Collection</code>

**Range**: <code>tree:Node</code>

### tree:search ### {#search}

Links a <code>tree:Node</code> to a <code>hydra:IriTemplate</code>. The search form will search the remaining items of the node.

**Domain**: <code>tree:Node</code>

**Range**: <code>hydra:IriTemplate</code>

### tree:shape ### {#shape}

The SHACL shape the members of the collection adhere to.

**Domain**: <code>tree:Collection</code>

**Range**: <code>sh:NodeShape</code>

### tree:member ### {#member}

Links to the collection’s items that are the <code>sh:targetNode</code>s of the SHACL shape defined with <code>tree:shape</code>.

**Domain**: <code>tree:Collection</code>

### tree:import ### {#import}

Imports a document containing triples needed for complying to the SHACL shape, or for evaluating the relation.

### tree:conditionalImport ### {#conditionalImport}

Imports a document only when the client is interesting in a specific <code>tree:path</code>.

### tree:zoom ### {#zoom}

A search form parameter: the zoom level of the tile cfr. OSM convention.

As defined by [Slippy Map Tilenames in OpenStreetMap](https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames)

### tree:longitudeTile ### {#longitudeTile}

A search form parameter: the X tile number from longitude cfr. OSM convention.

As defined by [Slippy Map Tilenames in OpenStreetMap](https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames)

### tree:latitudeTile ### {#latitudeTile}

A search form parameter: the Y tile number from latitude cfr. OSM convention.

As defined by [Slippy Map Tilenames in OpenStreetMap](https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames)

### tree:timeQuery ### {#timeQuery}

A search form parameter: accompagnied by a <code>tree:path</code>, it indicates the property on which a time search can be done

### tree:viewDescription ### {#viewDescription}

Links together any HTTP response with a view description on which things like retention policies, contact information of a server, etc. can be found.

**Domain**: <code>tree:Node</code>
