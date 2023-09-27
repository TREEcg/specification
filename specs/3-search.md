# Search forms # {#searching}

Searching through a TREE will allow you to immediately jump to the right <code>tree:Node</code>.
TREE relies on the [Hydra search specification](http://www.hydra-cg.com/spec/latest/core/#hydra:search) for its search forms.
It does however extend Hydra with specific search properties (<code>hydra:IriTemplate</code>) for different types of search forms, and searches starting from a <code>tree:ViewDescription</code>, to which the search form is linked with <code>tree:search</code>.
The behaviour of the search form fully depends on the specific property, for which TREE introduces a couple of specific properties:

## Geospatial XYZ tiles search form ## {#xyztiles}

Three properties allow to specify a geospatial XYZ tiles template (also known as slippy maps).
 1. <code>tree:longitudeTile</code> describes the X value
 2. <code>tree:latitudeTile</code> descrbes the Y value
 3. <code>tree:zoom</code> describes the zoom level

All properties expect positive integers.

<div class="example">
    ```turtle
    <https://tiles.openplanner.team/#LatestCollection> a tree:Collection ;
        dcterms:title "A prototype tree:Collection for Linked OpenStreetMap’s roads"@en ;
        tree:view <https://tiles.openplanner.team/planet/20201103-095900/14/8411/5485> .

    <https://tiles.openplanner.team/planet/20201103-095900/14/8411/5485> a tree:Node ;
        tree:viewDescription <https://tiles.openplanner.team/planet/> .

    <https://tiles.openplanner.team/planet/> a tree:ViewDescription ;
        tree:search [
            a hydra:IriTemplate ;
            hydra:template "https://tiles.openplanner.team/planet/20201103-095900/{z}/{x}/{y}" ;
            hydra:variableRepresentation hydra:BasicRepresentation ;
            hydra:mapping [
                a hydra:IriTemplateMapping ;
                hydra:variable "x";
                hydra:property tree:longitudeTile;
                hydra:required true
            ],[
                a hydra:IriTemplateMapping ;
                hydra:variable "y";
                hydra:property tree:latitudeTile;
                hydra:required true
            ],[
                a hydra:IriTemplateMapping ;
                hydra:variable "z";
                hydra:property tree:zoom;
                hydra:required true
            ]
        ] .
        
    ```
</div>

This search form describes a specific search form that uses a quad tree. The zoom level describes the depth, the longitudeTile and latitudeTile describe the x and y index of the fragmentation. (e.g., on zoom level 0, there’s 1 tile, on zoom level 1, there are 4 tiles, etc.).

## Searching through a list of objects ordered by time ## {#timesearch}

Same as the previous example but with the predicate <code>tree:timeQuery</code> expecting an <code>xsd:dateTime</code>.
This time however, when the page itself does not exist, a redirect is doing to happen to the page containing the timestamp.
A <code>tree:path</code> can indicate the time predicate which is intended.

<div class="example">
    ```turtle
    <https://example.org/#Collection> a tree:Collection ;
        dcterms:title "An example collection with a time search view"@en ;
        tree:view <https://example.org/Node1> .

    <https://example.org/Node1> a tree:Node ;
        tree:viewDescription <https://example.org/#TimeSearch> .

    <https://example.org/#TimeSearch> a tree:ViewDescription ;
        tree:search [
            a hydra:IriTemplate ;
            hydra:template "https://example.org/{generatedAt}" ;
            hydra:variableRepresentation hydra:BasicRepresentation ;
            hydra:mapping [
                a hydra:IriTemplateMapping ;
                hydra:variable "generatedAt";
                tree:path prov:generatedAtTime;
                hydra:property tree:timeQuery;
                hydra:required true
            ]
        ] .
    ```
</div>
