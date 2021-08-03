# Vocabulary # {#vocabulary}

**Namespace**: `https://w3id.org/tree#`

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

A collection has members that may adhere to a certain shape.

### tree:Node ### {#Node}

A `tree:Node` is a node that may contain links to other dereferenceable resources that lead to a full overview of a `tree:Collection`.

### tree:Relation ### {#Relation}

An entity that describes a relation between two `tree:Nodes`.

The `tree:Relation` has specific sub-classes that implement a more specific type between the values. These types are described in the ontology (all classes are `rdf:subClassOf` `tree:Relation`):
 - String, Date or Number comparison:
     - `tree:PrefixRelation` — All elements in the related node have this prefix
     - `tree:SubstringRelation` — All elements in the related node have this substring
     - `tree:SuffixRelation` — All members of this related node end with this suffix
     - `tree:GreaterThanRelation` — the related Node’s members are greater than the value. For string comparison, this relation can refer to a comparison configuration
     - `tree:GreaterThanOrEqualToRelation` — similar to ↑
     - `tree:LessThanRelation`
     - `tree:LessThanOrEqualToRelation`
     - `tree:EqualThanRelation`
 - Geo-spatial comparison (requires the node values to be WKT-strings): 
     - `tree:GeospatiallyContainsRelation` — (for semantics, see the [DE-9IM wikipedia page](https://en.wikipedia.org/wiki/DE-9IM))

### tree:ConditionalImport ### {#ConditionalImport}

A class to import a file or a stream based on a `tree:path` of properties. This way it can import the necessary data for complying to the SHACL shape, or evaluating a relation type.

## Properties ## {#properties}

### tree:relation ### {#relation}

Links a node to a relation

**Domain**: `tree:Node`

**Range**: `tree:Relation`


### tree:remainingItems ### {#remainingItems}

Remaining number of items of this node, the items in its children included.

**Domain**: `tree:Relation`

**Range**: `xsd:integer`

### tree:node ### {#node}

The URL to be derefenced when this relation cannot be pruned.

**Domain**: `tree:Relation`

**Range**: `tree:Node`

### tree:value ### {#value}

The contextual value of this node: may contain e.g., a WKT-string with the bound of a rectangle, may contain a string, an integer, or even link to another resource where clear comparison rules apply.

**Domain**: `tree:Relation`

### tree:path ### {#path}

A property path, [as defined by SHACL](https://www.w3.org/TR/shacl/#x2.3.1-shacl-property-paths), that indicates what resource the `tree:value` affects.

See [](#relations)

**Domain**: `tree:Relation`

### tree:view ### {#view}

Links the collection to a `tree:Node` from which all members can be found. If only a part of the collection’s members can be found from that point on, only use `dcterms:isPartOf` or `void:subset`.

**Domain**: `tree:Collection`

**Range**: `tree:Node`

### tree:search ### {#search}

Links a `tree:Node` to a `hydra:IriTemplate`. The search form will search the remaining items of the node.

**Domain**: `tree:Node`

**Range**: `hydra:IriTemplate`

### tree:shape ### {#shape}

The SHACL shape the members of the collection adhere to.

**Domain**: `tree:Collection`

**Range**: `sh:NodeShape`

### tree:member ### {#member}

Links to the collection’s items that are the `sh:targetNode`s of the SHACL shape defined with `tree:shape`.

**Domain**: `tree:Collection`

### tree:import ### {#import}

Imports a document containing triples needed for complying to the SHACL shape, or for evaluating the relation.

### tree:conditionalImport ### {#conditionalImport}

Imports a document only when the client is interesting in a specific `tree:path`.

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

A search form parameter: accompagnied by a `tree:path`, it indicates the property on which a time search can be done
