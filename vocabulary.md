# Vocabulary # {#voc}

**Namespace**: `https://w3id.org/tree#`

Prefixes:

```turtle
@prefix tree: <https://w3id.org/tree#>.
@prefix foaf: <http://xmlns.com/foaf/0.1/>.
@prefix hydra: <http://www.w3.org/ns/hydra/core#>.
@prefix schema: <http://schema.org/>.
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.
```

## Classes ## {#classes}

### tree:Collection ### {#collection}

### tree:Node ### {#Node}

A `tree:Node` is a node that may contain links to other dereferenceable resources that lead to a full overview of a `hydra:Collection`.

### tree:Relation ### {#Relation}

An entity that describes a relation between two `tree:Nodes`.

The `tree:Relation` has specific sub-classes that implement a more specific type between the values. These types are described in the ontology (all classes are `rdf:subClassOf` `tree:Relation`):
 - String, Date or Number comparison:
   - `tree:PrefixRelation` - All elements in the related node have this prefix
   - `tree:SubstringRelation` - All elements in the related node have this substring
   - `tree:GreaterThanRelation` - the related Node’s members are greater than the value. For string comparison, this relation can refer to a comparison configuration
   - `tree:GreaterOrEqualThanRelation` - similar to ↑
   - `tree:LessThanRelation`
   - `tree:LessOrEqualThanRelation`
   - `tree:EqualThanRelation`
 - Geo-spatial comparison (requires the node values to be WKT-strings): 
   - `tree:GeospatiallyContainsRelation` (for semantics, see https://en.wikipedia.org/wiki/DE-9IM)
 - Interval comparison
   - `tree:InBetweenRelation`
   
*Let us know in an issue if you want another type to be added to this official list*

### tree:ConditionalImport ### {#ConditionalImport}

## Properties ## {#properties}

### tree:relation ### {#relation}

**Domain**: tree:Node
**Range**: tree:Relation


### tree:remainingItems ### {#remainingItems}

Remaining number of items of this node plus its children.

**Domain**: tree:Node
**Range**: xsd:integer

### tree:node ### {#node}

The URL to be derefenced when this relation cannot be pruned.

**Domain**: tree:Relation
**Range**: tree:Node

### tree:value ### {#value}

The contextual value of this node: may contain e.g., a WKT-string with the bound of a rectangle, may contain a string, an integer, or even link to another resource where clear comparison rules apply.

**Domain**: tree:Relation

### tree:path ### {#path}

A property path, as defined by shacl, that indicates what resource the `tree:value` affects.

See https://github.com/pietercolpaert/TreeOntology/blob/master/specs/2-traversing.md

**Domain**: tree:Relation

### tree:view ### {#view}

Links the collection to a `tree:Node` from which all other members can be found. If only a part of the collection’s members can be found from that point on, only use `dcterms:isPartOf` or `void:subset`.

**Domain**: hydra:Collection

**Range**: tree:Node

### tree:member ### {#member}

### tree:import ### {#import}

### tree:conditionalImport ### {#conditionalImport}

### tree:shape ### {#shape} 

### tree:qualifiedValue ### {#qualifiedValue}

### tree:zoom ### {#zoom}

The zoom level of the tile cfr. OSM convention

As defined by: https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames

### tree:longitudeTile ### {#longitudeTile}

The X tile number from longitude cfr. OSM convention

As defined by: https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames


### tree:latitudeTile ### {#latitudeTile}

The Y tile number from latitude cfr. OSM convention

As defined by: https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames

### tree:timeQuery ### {#timeQuery}

Accompagnied by a tree:path, indicates the property on which a time search can be done
