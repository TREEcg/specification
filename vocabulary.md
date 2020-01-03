# Tree Ontology: the vocabulary

Prefixes:

```turtle
@prefix tree: <https://w3id.org/tree#>.
@prefix foaf: <http://xmlns.com/foaf/0.1/>.
@prefix hydra: <http://www.w3.org/ns/hydra/core#>.
@prefix schema: <http://schema.org/>.
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.
```

### Classes

#### tree:Node

A `tree:Node` is a node that may contain links to other dereferenceable resources that lead to a full overview of a `hydra:Collection`.

#### tree:Relation

An entity that describes a relation between two `tree:Nodes`.

The `tree:Relation` has specific sub-classes that implement a more specific type between the values. These types are described in the ontology (all classes are `rdf:subClassOf` `tree:Relation`):
 - String, Date or Number comparison:
   - `tree:PrefixRelation` - All elements in the related node have this prefix
   - `tree:SubstringRelation` - All elements in the related node have this substring
   - `tree:GreaterThanRelation` - the related Node’s members are greater than the value. For string comparison, this relation can refer to a comparison configuration
   - `tree:GreaterOrEqualThanRelation` - similar to ↑
   - `tree:LesserThanRelation`
   - `tree:LesserOrEqualThanRelation`
   - `tree:EqualThanRelation`
 - Geo-spatial comparison (requires the node values to be WKT-strings): 
   - `tree:GeospatiallyContainsRelation` (for semantics, see [DE-9IM](https://en.wikipedia.org/wiki/DE-9IM))
 - Interval comparison
   - `tree:InBetweenRelation`
   
_Let us know in an issue if you want another type to be added to this official list_

### Properties

#### tree:relation

__Domain__: tree:Node
__Range__: tree:Relation


#### tree:remainingItems

Remaining number of items of this node plus its children.

__Domain__: tree:Node
__Range__: xsd:integer

#### tree:node

The URL to be derefenced when this relation cannot be pruned.

__Domain__: tree:Relation
__Range__: tree:Node

#### tree:value

The contextual value of this node: may contain e.g., a WKT-string with the bound of a rectangle, may contain a string, an integer, or even link to another resource where clear comparison rules apply.

__Domain__: tree:Relation

#### tree:path

A property path, as defined by shacl, that indicates what resource the `tree:value` affects.

See https://github.com/pietercolpaert/TreeOntology/blob/master/specs/2-traversing.md

__Domain__: tree:Relation

#### tree:zoom

The zoom level of the tile cfr. OSM convention

As defined by: https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames

#### tree:longitudeTile

The X tile number from longitude cfr. OSM convention

As defined by: https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames


#### tree:latitudeTile

The Y tile number from latitude cfr. OSM convention

As defined by: https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames
