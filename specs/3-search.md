# Searching through the collection # {#searching}

Searching through a TREE will allow you to immediately jump to the right `tree:Node`.
TREE relies on the [Hydra search specification](http://www.hydra-cg.com/spec/latest/core/#hydra:search) for its search forms.
It does however extend Hydra with specific search properties (`hydra:IriTemplate`) for different types of search forms, and searches starting from a `tree:Node`, to which the search form is linked with `tree:search`.
The behaviour of the search form fully depends on the specific property, for which TREE introduces a couple of specific properties:

## Geospatial XYZ tiles search form ## {#xyztiles}

Three properties allow to specify a geospatial XYZ tiles template (also known as slippy maps).
 1. `tree:longitudeTile` describes the X value
 2. `tree:latitudeTile` descrbes the Y value
 3. `tree:zoom` describes the zoom level

All properties expect positive integers.

Example:
```json
{
   "@context": {
     "viewOf" : {
       "@reverse": "tree:view",
       "@type": "@id"
     },
     ...
   },
  "viewOf": {
    "@id": "https://tiles.openplanner.team/planet/",
    "@type": "tree:Collection",
    "dcterms:license": "http://opendatacommons.org/licenses/odbl/1-0/",
    "dcterms:rights": "http://www.openstreetmap.org/copyright",
    "tree:member": [ ..., ... ]
  },
  "tree:search": {
    "@type": "hydra:IriTemplate",
    "hydra:template": "https://tiles.openplanner.team/planet/{z}/{x}/{y}",
    "hydra:variableRepresentation": "hydra:BasicRepresentation",
    "hydra:mapping": [
      {
        "@type": "hydra:IriTemplateMapping",
        "hydra:variable": "x",
        "hydra:property": "tree:longitudeTile",
        "hydra:required": true
      },
      {
        "@type": "hydra:IriTemplateMapping",
        "hydra:variable": "y",
        "hydra:property": "tree:latitudeTile",
        "hydra:required": true
      },
      {
        "@type": "hydra:IriTemplateMapping",
        "hydra:variable": "z",
        "hydra:property": "tree:zoom",
        "hydra:required": true
      }
    ]
  }
}
```

This search form describes a specific search form that uses a quad tree. The zoom level describes the depth, the longitudeTile and latitudeTile describe the x and y index of the fragmentation. (e.g., on zoom level 0, there’s 1 tile, on zoom level 1, there are 4 tiles, etc.).

## Searching through a list of objects ordered by time ## {#timesearch}

Same as the previous example but with the predicate `tree:timeQuery` expecting an `xsd:dateTime`.
This time however, when the page itself does not exist, a redirect is doing to happen to the page containing the timestamp.
A `tree:path` can indicate the time predicate which is intended.

Example:
```json
{
   "@context": {
     ...
   },
  "dcterms:isPartOf": {
    "@id": "#coll",
    "@type": "tree:Collection",
    "tree:member": [ ..., ... ]
  },
  "tree:search": {
    "@type": "hydra:IriTemplate",
    "hydra:template": "https://example.org/{t}",
    "hydra:variableRepresentation": "hydra:BasicRepresentation",
    "hydra:mapping": [
      {
        "@type": "hydra:IriTemplateMapping",
        "hydra:variable": "t",
        "tree:path": "prov:generatedAtTime",
        "hydra:property": "tree:timeQuery",
        "hydra:required": true
      }
    ]
  }
}
```
