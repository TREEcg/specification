# Searching through the collection

Searching through a Tree will allow you to immediately jump to the right `tree:Node`.

## 1. Geospatial tiles search form

Example:
```json
{
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

This search form describes

## 2. Searching through an ordered list of objects

### 2.1 Time series

### 2.2 Linked Connections

## 3. Skip Graphs

Using this specification, you can as well model skip graphs. //TODO: Harm

## 4. Combining building blocks

At all times, we need to be able to combine building blocks.
