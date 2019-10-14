# Searching through the collection

Searching through a Tree will allow you to immediately jump to the right `tree:Node`.

The `tree:Node` in this case does not need to be explicitly defined. However, there __must__ be a property linking the current page URL to the URI of the `hydra:Collection`. Three properties may be used:
 1. `<collection> hydra:view <currentPageUrl> .`: may be used _only_ in the case when the _entire_ `hydra:Collection` can be found starting from this page only.
 2. `<collection> void:subset <currentPageUrl> .`
 3. `<currentPageUrl> dcterms:isPartOf <collection> .`

The collection itself then __must__ have a `hydra:search` property, describing a search form. For different use cases, the Tree Ontology specifies different search forms (add your own through a pull request):

## 1. Geospatial tiles search form

Example:
```json
{
   "@context": {
     ...
   },
  "@id": "https://tiles.openplanner.team/planet/14/8411/5485/",
  "tiles:zoom": 14,
  "tiles:longitudeTile": 8411,
  "tiles:latitudeTile": 5485,
  "dcterms:isPartOf": {
    "@id": "https://tiles.openplanner.team/planet/",
    "@type": "hydra:Collection",
    "dcterms:license": "http://opendatacommons.org/licenses/odbl/1-0/",
    "dcterms:rights": "http://www.openstreetmap.org/copyright",
    "hydra:search": {
      "@type": "hydra:IriTemplate",
      "hydra:template": "https://tiles.openplanner.team/planet/{z}/{x}/{y}",
      "hydra:variableRepresentation": "hydra:BasicRepresentation",
      "hydra:mapping": [
        {
          "@type": "hydra:IriTemplateMapping",
          "hydra:variable": "x",
          "hydra:property": "tiles:longitudeTile",
          "hydra:required": true
        },
        {
          "@type": "hydra:IriTemplateMapping",
          "hydra:variable": "y",
          "hydra:property": "tiles:latitudeTile",
          "hydra:required": true
        },
        {
          "@type": "hydra:IriTemplateMapping",
          "hydra:variable": "z",
          "hydra:property": "tiles:zoom",
          "hydra:required": true
        }
      ]
    }
  }
}
```

This search form describes a specific search form that uses a quad tree. The zoom level describes the depth, the longitudeTile and latitudeTile describe the x and y index of the fragmentation. (e.g., on zoom level 0, there’s 1 tile, on zoom level 1, there are 4 tiles, etc.)

#### Compliance

This SPARQL query can be used to discover a geospatial search form. Mind that the ?currentUrl variable needs to be filled out.

```sparql
PREFIX void: <http://rdfs.org/ns/void#>
PREFIX hydra: <http://www.w3.org/ns/hydra/core#>
SELECT * WHERE {
    ?collection void:subset|hydra:view|^dcterms:isPartOf ?currentUrl .
}
```

You can test this query on the [Comunica Playground](http://query.linkeddatafragments.org/#datasources=https%3A%2F%2Ftiles.openplanner.team%2Fplanet%2F14%2F8411%2F5485%2F&query=PREFIX%20dcterms%3A%20%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0APREFIX%20void%3A%20%3Chttp%3A%2F%2Frdfs.org%2Fns%2Fvoid%23%3E%0APREFIX%20hydra%3A%20%3Chttp%3A%2F%2Fwww.w3.org%2Fns%2Fhydra%2Fcore%23%3E%0A%0ASELECT%20*%20WHERE%20%7B%0A%20%20%20%20%3Fcollection%20void%3Asubset%7Chydra%3Aview%7C%5Edcterms%3AisPartOf%20%3FcurrentUrl%20.%0A%7D).

### 2. Searching through an ordered list of objects

#### 2.1 Time series

#### 2.2 Linked Connections

### 3. Skip Graphs

Using this specification, you can as well model skip graphs. //TODO: Harm

