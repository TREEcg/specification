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

__subClassOf__: hydra:PartialCollectionView

A tree:Node is a node that may contain links to other dereferenceable resources, may contain the actual data (as part of a named graph, or using the predicate hydra:member).

#### tree:ChildRelation

An entity that describes a specific Parent-Child relation between two tree:Nodes.

The ChildRelation has specific sub-classes that implement a more specific type between the values. These types are described in the ontology (all classes are rdf:subClassOf tree:ChildRelation):
 - String, Date or Number comparison:
   - tree:StringCompletesRelation - The parent value needs to be concatenated with this node’s value
   - tree:GreaterThanRelation - the child is greater than the value. For string comparison, this relation can refer to a comparison configuration
   - tree:GreaterOrEqualThanRelation - similar to ↑
   - tree:LesserThanRelation
   - tree:LesserOrEqualThanRelation
   - tree:EqualThanRelation
 - Geo-spatial comparison (requires the node values to be WKT-strings): 
   - tree:GeospatiallyContainsRelation (for semantics, see [DE-9IM](https://en.wikipedia.org/wiki/DE-9IM))
 - Interval comparison
   - tree:InBetweenRelation
   
_Let us know in an issue if you want another type to be added to this official list_

### Properties

#### tree:childRelation

__Domain__: tree:Node
__Range__: tree:ChildRelation


#### tree:remainingItems

Remaining number of items of this node plus its children.

__Domain__: tree:Node
__Range__: xsd:integer

#### tree:child

The parent node has a child with a certain relation (defined by tree:relationToParentValue). If the order of the children is important, use an rdf:List instead of using the property multiple times.

__Domain__: tree:ChildRelation
__Range__: tree:Node

#### tree:value

The contextual value of this node: may contain e.g., a WKT-string with the bound of a rectangle, may contain a string

__Domain__: tree:Node

#### tree:zoom

The zoom level of the tile cfr. OSM convention

As defined by: https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames

#### tree:longitudeTile

The X tile number from longitude cfr. OSM convention

As defined by: https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames


#### tree:latitudeTile

The Y tile number from latitude cfr. OSM convention

As defined by: https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames
