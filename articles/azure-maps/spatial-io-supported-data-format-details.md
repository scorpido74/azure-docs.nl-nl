---
title: Details van de ondersteunde gegevensnotatie | Microsoft Azure Maps
description: Ontdek hoe afgebakende ruimtelijke gegevens worden ontleed in de ruimtelijke IO-module.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3353620f1751e939a04543115fe704555fb3bc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334086"
---
# <a name="supported-data-format-details"></a>Details van ondersteunde gegevensindeling

In dit artikel vindt u details over de lees- en schrijfondersteuning voor alle XML-tags en bekende tekstgeometrietypen. Ook wordt beschreven hoe de afgebakende ruimtelijke gegevens worden ontleed in de ruimtelijke IO-module.

## <a name="supported-xml-namespaces"></a>Ondersteunde XML-naamruimten

De ruimtelijke IO-module ondersteunt XML-tags uit de volgende naamruimten.

| Voorvoegsel naamruimte | Naamruimte URI   | Opmerkingen                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Lees alleen ondersteuning in GeoRSS-bestanden.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Lees alleen ondersteuning in GeoRSS-bestanden.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Lees alleen ondersteuning in GPX-bestanden. Parses en maakt gebruik van DisplayColor. Alle andere eigenschappen die zijn toegevoegd aan shapemetagegevens. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Ondersteund in GPX-bestanden. Gebruikt lijnkleur. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Alleen lezen. GeoRSS schrijft met behulp van Atom-formaat.              |

## <a name="supported-xml-elements"></a>Ondersteunde XML-elementen

De ruimtelijke IO-module ondersteunt de volgende XML-elementen. Xml-tags die niet worden ondersteund, worden omgezet in een JSON-object. Vervolgens wordt elke tag toegevoegd als `properties` een eigenschap in het veld van de bovenliggende vorm of laag.

### <a name="kml-elements"></a>KML-elementen

De ruimtelijke IO-module ondersteunt de volgende KML-elementen.

| Naam van element         | Lezen    | Schrijven   | Opmerkingen                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | Gedeeltelijke | ja     | Object wordt ontleed, maar wordt niet gebruikt voor het positioneren van de vorm.                                                                    |
| `AddressDetails`     | Gedeeltelijke | nee      | Object wordt ontleed, maar wordt niet gebruikt voor het positioneren van de vorm.                                                                    |
| `atom:author`        | ja     | ja     |                                                                                                                            |
| `atom:link`          | ja     | ja     |                                                                                                                            |
| `atom:name`          | ja     | ja     |                                                                                                                            |
| `BalloonStyle`       | Gedeeltelijke | Gedeeltelijke | `displayMode`wordt niet ondersteund. Omgezet naar `PopupTemplate`een . Als u wilt `popupTemplate` schrijven, voegt u een eigenschap toe als eigenschap van de functie waarvoor u deze wilt schrijven. |
| `begin`              | ja     | ja     |                                                                                                                            |
| `color`              | ja     | ja     | Inclusief `#AABBGGRR` `#BBGGRR`en . Geparseerd in een CSS-kleurtekenreeks                                                           |
| `colorMode`          | ja     | nee      |                                                                                                                            |
| `coordinates`        | ja     | ja     |                                                                                                                            |
| `Data`               | ja     | ja     |                                                                                                                            |
| `description`        | ja     | ja     |                                                                                                                            |
| `displayName`        | ja     | ja     |                                                                                                                            |
| `Document`           | ja     | ja     |                                                                                                                            |
| `drawOrder`          | Gedeeltelijke | nee      | Lees voor grond overlays en gebruikt om ze te sorteren. 
| `east`               | ja     | ja     |                                                                                                                            |
| `end`                | ja     | ja     |                                                                                                                            |
| `ExtendedData`       | ja     | ja     | `Data`Ondersteunt niet-getypte , `SimpleData` of `Schema`entiteitsvervangingen van het formulier `$[dataName]`.                      |
| `extrude`            | Gedeeltelijke | Gedeeltelijke | Alleen ondersteund voor polygonen. MultiGeometry met veelhoeken van verschillende hoogtes wordt uitgesplitst naar afzonderlijke functies. Lijnstijlen worden niet ondersteund. Polygonen met een hoogte van 0 worden weergegeven als een vlakke veelhoek. Bij het lezen wordt de hoogte van de eerste coördinaat in de buitenring toegevoegd als hoogteeigenschap van de veelhoek. Vervolgens wordt de hoogte van de eerste coördinaat gebruikt om de veelhoek op de kaart weer te geven. |
| `fill`               | ja     | ja     |                                                                                                                            |
| `Folder`             | ja     | ja     |                                                                                                                            |
| `GroundOverlay`      | ja     | ja     | `color`wordt niet ondersteund                                                                                                   |
| `heading`            | Gedeeltelijke | nee      | Geparseerd, maar `SimpleDataLayer`niet weergegeven door . Hiermee wordt alleen geschreven als gegevens worden opgeslagen in de eigenschap van de vorm.                 |
| `hotSpot`            | ja     | Gedeeltelijke | Hiermee wordt alleen geschreven als gegevens worden opgeslagen in de eigenschap van de vorm. Eenheden worden alleen als 'pixels' uitgevoerd.                         |
| `href`               | ja     | ja     |                                                                                                                            |
| `Icon`               | Gedeeltelijke | Gedeeltelijke | Geparseerd, maar `SimpleDataLayer`niet weergegeven door . Hiermee wordt alleen de eigenschap icon van de vorm geschreven als deze een URI-gegevens bevat. Alleen `href` wordt ondersteund. |
| `IconStyle`          | Gedeeltelijke | Gedeeltelijke | `icon`, `heading` `colorMode`, `hotspots` en waarden worden ontleed, maar ze worden niet weergegeven door`SimpleDataLayer`         |
| `innerBoundaryIs`    | ja     | ja     |                                                                                                                            |
| `kml`                | ja     | ja     |                                                                                                                            |
| `LabelStyle`         | nee      | nee      |                                                                                                                            |
| `LatLonBox`          | ja     | ja     |                                                                                                                            |
| `gx:LatLonQuad`      | ja     | ja     |                                                                                                                            |
| `LinearRing`         | ja     | ja     |                                                                                                                            |
| `LineString`         | ja     | ja     |                                                                                                                            |
| `LineStyle`          | ja     | ja     | `colorMode`wordt niet ondersteund.                                                                                         |
| `Link`               | ja     | nee      | Alleen `href` de eigenschap wordt ondersteund voor netwerkkoppelingen.                                                                   |
| `MultiGeometry`      | Gedeeltelijke | Gedeeltelijke | Kan worden uitgesplitst in individuele functies bij het lezen.                                                                     |
| `name`               | ja     | ja     |                                                                                                                            |
| `NetworkLink`        | ja     | nee      | Koppelingen moeten zich op hetzelfde domein bevinden als het document.                                                                  |
| `NetworkLinkControl` | nee      | nee      |                                                                                                                            |
| `north`              | ja     | ja     |                                                                                                                            |
| `open`               | ja     | ja     |                                                                                                                            |
| `outerBoundaryIs`    | ja     | ja     |                                                                                                                            |
| `outline`            | ja     | ja     |                                                                                                                            |
| `overlayXY`          | nee      | nee      |                                                                                                                            |
| `Pair`               | Gedeeltelijke | nee      | Alleen `normal` de stijl `StyleMap` in a wordt ondersteund. `highlight`wordt niet ondersteund.                                   |
| `phoneNumber`        | ja     | ja     |                                                                                                                            |
| `PhotoOverlay`       | nee      | nee      |                                                                                                                            |
| `Placemark`          | ja     | ja     |                                                                                                                            |
| `Point`              | ja     | ja     |                                                                                                                            |
| `Polygon`            | ja     | ja     |                                                                                                                            |
| `PolyStyle`          | ja     | ja     |                                                                                                                            |
| `Region`             | Gedeeltelijke | Gedeeltelijke | `LatLongBox`wordt ondersteund op documentniveau.                                                                      |
| `rotation`           | nee      | nee      |                                                                                                                            |
| `rotationXY`         | nee      | nee      |                                                                                                                            |
| `scale`              | nee      | nee      |                                                                                                                            |
| `Schema`             | ja     | ja     |                                                                                                                            |
| `SchemaData`         | ja     | ja     |                                                                                                                            |
| `schemaUrl`          | Gedeeltelijke | ja     | Ondersteunt geen laadstijlen van externe documenten die niet in een KMZ zijn opgenomen.                             |
| `ScreenOverlay`      | nee      | nee      |                                                                                                                            |
| `screenXY`           | nee      | nee      |                                                                                                                            |
| `SimpleData`         | ja     | ja     |                                                                                                                            |
| `SimpleField`        | ja     | ja     |                                                                                                                            |
| `size`               | nee      | nee      |                                                                                                                            |
| `Snippet`            | Gedeeltelijke | Gedeeltelijke | `maxLines`attribuut wordt genegeerd.                                                                                  |
| `south`              | ja     | ja     |                                                                                                                            |
| `Style`              | ja     | ja     |                                                                                                                            |
| `StyleMap`           | Gedeeltelijke | nee      | Alleen de normale `StyleMap` stijl in a wordt ondersteund.                                                                        |
| `styleUrl`           | Gedeeltelijke | ja     | URL's in externe stijl worden niet ondersteund.                                                                         |
| `text`               | ja     | ja     | Vervanging `$[geDirections]` van wordt niet ondersteund                                                                          |
| `textColor`          | ja     | ja     |                                                                                                                            |
| `TimeSpan`           | ja     | ja     |                                                                                                                            |
| `TimeStamp`          | ja     | ja     |                                                                                                                            |
| `value`              | ja     | ja     |                                                                                                                            |
| `viewRefreshMode`    | Gedeeltelijke | nee      |  Als u naar een WMS-service wijst, wordt deze alleen `onStop` ondersteund voor grondoverlays. Zal toevoegen `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` aan de URL en bijwerken als de kaart beweegt.  |
| `visibility`         | ja     | ja     |                                                                                                                            |
| `west`               | ja     | ja     |                                                                                                                            |
| `when`               | ja     | ja     |                                                                                                                            |
| `width`              | ja     | ja     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS-elementen

De ruimtelijke IO-module ondersteunt de volgende GeoRSS-elementen.

| Naam van element             | Lezen    | Schrijven | Opmerkingen                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | ja     | ja   |                                                                                                |
| `atom:category`          | ja     | ja   |                                                                                                |
| `atom:content`           | ja     | ja   |                                                                                                |
| `atom:contributor`       | ja     | ja   |                                                                                                |
| `atom:email`             | ja     | ja   |                                                                                                |
| `atom:entry`             | ja     | ja   |                                                                                                |
| `atom:feed`              | ja     | ja   |                                                                                                |
| `atom:icon`              | ja     | ja   |                                                                                                |
| `atom:id`                | ja     | ja   |                                                                                                |
| `atom:link`              | ja     | ja   |                                                                                                |
| `atom:logo`              | ja     | ja   |                                                                                                |
| `atom:name`              | ja     | ja   |                                                                                                |
| `atom:published`         | ja     | ja   |                                                                                                |
| `atom:rights`            | ja     | ja   |                                                                                                |
| `atom:source`            | ja     | ja   |                                                                                                |
| `atom:subtitle`          | ja     | ja   |                                                                                                |
| `atom:summary`           | ja     | ja   |                                                                                                |
| `atom:title`             | ja     | ja   |                                                                                                |
| `atom:updated`           | ja     | ja   |                                                                                                |
| `atom:uri`               | ja     | ja   |                                                                                                |
| `geo:lat`                | ja     | nee    | Geschreven als `georss:point`een .                                                                   |
| `geo:lon`                | ja     | nee    | Geschreven als `georss:point`een .                                                                   |
| `geo:long`               | ja     | nee    | Geschreven als `georss:point`een .                                                                   |
| `georss:box`             | ja     | nee    | Lees als een veelhoek en gegeven een `subType` eigenschap van "Rechthoek"                                |
| `georss:circle`          | ja     | ja   |                                                                                                |
| `georss:elev`            | ja     | ja   |                                                                                                |
| `georss:featurename`     | ja     | ja   |                                                                                                |
| `georss:featuretypetag`  | ja     | ja   |                                                                                                |
| `georss:floor`           | ja     | ja   |                                                                                                |
| `georss:line`            | ja     | ja   |                                                                                                |
| `georss:point`           | ja     | ja   |                                                                                                |
| `georss:polygon`         | ja     | ja   |                                                                                                |
| `georss:radius`          | ja     | ja   |                                                                                                |
| `georss:relationshiptag` | ja     | ja   |                                                                                                |
| `georss:where`           | ja     | ja   |                                                                                                |
| `geourl:latitude`        | ja     | nee    | Geschreven als `georss:point`een .                                                                   |
| `geourl:longitude`       | ja     | nee    | Geschreven als `georss:point`een .                                                                   |
| `position`               | ja     | nee    | Sommige XML-feeds verpakken GML met een positietag `georss:where` in plaats van deze te verpakken met een tag. Zal deze tag lezen, maar `georss:where` zal schrijven met behulp van een tag. |
| `rss`                    | ja     | nee    | GeoRSS geschreven in ATO-formaat.                                                                 |
| `rss:author`             | ja     | Gedeeltelijke | Geschreven als `atom:author`een .                                                                 |
| `rss:category`           | ja     | Gedeeltelijke | Geschreven als `atom:category`een .                                                               |
| `rss:channel`            | ja     | nee    |                                                                                                |
| `rss:cloud`              | ja     | nee    |                                                                                                |
| `rss:comments`           | ja     | nee    |                                                                                                |
| `rss:copyright`          | ja     | Gedeeltelijke | Geschreven als `atom:rights` een if vorm `rights` `properties` heeft nog geen eigenschap.       |
| `rss:description`        | ja     | Gedeeltelijke | Geschreven als `atom:content` een if vorm `content` `properties` heeft nog geen eigenschap.      |
| `rss:docs`               | ja     | nee    |                                                                                                |
| `rss:enclosure`          | ja     | nee    |                                                                                                |
| `rss:generator`          | ja     | nee    |                                                                                                |
| `rss:guid`               | ja     | Gedeeltelijke | Geschreven als `atom:id` een if vorm `id` `properties` heeft nog geen eigenschap.         |
| `rss:image`              | ja     | Gedeeltelijke | Geschreven als `atom:logo` een if vorm `logo` `properties` heeft nog geen eigenschap.      |
| `rss:item`               | ja     | Gedeeltelijke | Geschreven als `atom:entry`een .                                                                  |
| `rss:language`           | ja     | nee    |                                                                                                |
| `rss:lastBuildDate`      | ja     | Gedeeltelijke | Geschreven als `atom:updated` een if vorm `updated` `properties` heeft nog geen eigenschap.     |
| `rss:link`               | ja     | Gedeeltelijke | Geschreven als `atom:link`een .                                                                   |
| `rss:managingEditor`     | ja     | Gedeeltelijke | Geschreven als `atom:contributor`een .                                                            |
| `rss:pubDate`            | ja     | Gedeeltelijke | Geschreven als `atom:published` een if vorm `published` `properties` heeft nog geen eigenschap.  |
| `rss:rating`             | ja     | nee    |                                                                                                |
| `rss:skipDays`           | ja     | nee    |                                                                                                |
| `rss:skipHours`          | ja     | nee    |                                                                                                |
| `rss:source`             | ja     | Gedeeltelijke | Geschreven als `atom:source` een `atom:link`met een .                                       |
| `rss:textInput`          | ja     | nee    |                                                                                                |
| `rss:title`              | ja     | Gedeeltelijke | Geschreven als `atom:title`een .                                                                  |
| `rss:ttl`                | ja     | nee    |                                                                                                |
| `rss:webMaster`          | ja     | nee    |                                                                                                |

### <a name="gml-elements"></a>GML-elementen

De ruimtelijke IO-module ondersteunt de volgende GML-elementen. 

| Naam van element            | Lezen | Schrijven | Opmerkingen                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | ja  | nee    | Geschreven `gml:posList`als .                                                              |
| `gml:curveMember`       | ja  | nee    |                                                                                        |
| `gml:curveMembers`      | ja  | nee    |                                                                                        |
| `gml:Box`               | ja  | nee    | Geschreven `gml:Envelope`als .                                                             |
| `gml:description`       | ja  | ja   |                                                                                        |
| `gml:Envelope`          | ja  | ja   |                                                                                        |
| `gml:exterior`          | ja  | ja   |                                                                                        |
| `gml:Feature`           | ja  | nee    | Geschreven als een vorm.                                                                    |
| `gml:FeatureCollection` | ja  | nee    | Geschreven als een geometrie collectie.                                                      |
| `gml:featureMember`     | ja  | nee    | Geschreven als een geometrie collectie.                                                      |
| `gml:geometry`          | ja  | nee    | Geschreven als een vorm.                                                                    |
| `gml:geometryMember`    | ja  | ja   |                                                                                        |
| `gml:geometryMembers`   | ja  | ja   |                                                                                        |
| `gml:identifier`        | ja  | ja   |                                                                                        |
| `gml:innerBoundaryIs`   | ja  | nee    | Geschreven `gml.interior`met behulp van .                                                          |
| `gml:interior`          | ja  | ja   |                                                                                        |
| `gml:LinearRing`        | ja  | ja   |                                                                                        |
| `gml:LineString`        | ja  | ja   |                                                                                        |
| `gml:lineStringMember`  | ja  | ja   |                                                                                        |
| `gml:lineStringMembers` | ja  | nee    |                                                                                        |
| `gml:MultiCurve`        | ja  | nee    | Alleen `gml:LineString` leest leden. Geschreven als`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | Gedeeltelijke  | Gedeeltelijke   | Alleen lezen als een FeatureCollection.                                              |
| `gml:MultiLineString`   | ja  | ja   |                                                                                        |
| `gml:MultiPoint`        | ja  | ja   |                                                                                        |
| `gml:MultiPolygon`      | ja  | ja   |                                                                                        |
| `gml:MultiSurface`      | ja  | nee    | Alleen `gml:Polygon` leest leden. Geschreven als`gml.MultiPolygon`                        |
| `gml:name`              | ja  | ja   |                                                                                        |
| `gml:outerBoundaryIs`   | ja  | nee    | Geschreven `gml.exterior`met behulp van .                                                          |
| `gml:Point`             | ja  | ja   |                                                                                        |
| `gml:pointMember`       | ja  | ja   |                                                                                        |
| `gml:pointMembers`      | ja  | nee    |                                                                                        |
| `gml:Polygon`           | ja  | ja   |                                                                                        |
| `gml:polygonMember`     | ja  | ja   |                                                                                        |
| `gml:polygonMembers`    | ja  | nee    |                                                                                        |
| `gml:pos`               | ja  | ja   |                                                                                        |
| `gml:posList`           | ja  | ja   |                                                                                        |
| `gml:surfaceMember`     | ja  | ja   |                                                                                        |

#### <a name="additional-notes"></a>aanvullende opmerkingen

- Lid-elementen worden gezocht naar een geometrie die kan worden begraven in onderliggende elementen. Deze zoekbewerking is noodzakelijk omdat veel XML-indelingen die zich uitstrekken van GML een geometrie mogelijk niet plaatsen als een direct onderliggend element van een lidelement.
- `srsName`wordt gedeeltelijk ondersteund voor WGS84-coördinaten en de volgende codes:[EPSG:4326](https://epsg.io/4326)), en web Mercator[(EPSG:3857](https://epsg.io/3857) of een van de alternatieve codes. Elk ander coördinaatsysteem wordt ontleed als WGS84 as-is.
- Tenzij opgegeven bij het lezen van een XML-feed, wordt de asvolgorde bepaald op basis van hints in de XML-feed. Er wordt een voorkeur gegeven voor de asvolgorde "breedte, lengte".
- Tenzij een aangepaste GML-naamruimte is opgegeven voor de eigenschappen bij het schrijven naar een GML-bestand, worden geen aanvullende eigenschapsinformatie toegevoegd.

### <a name="gpx-elements"></a>GPX-elementen

De ruimtelijke IO-module ondersteunt de volgende GPX-elementen.

| Naam van element             | Lezen    | Schrijven   | Opmerkingen                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | ja     | ja     |                                                                                             |
| `gpx:author`             | ja     | ja     |                                                                                             |
| `gpx:bounds`             | ja     | ja     | Omgezet in een LocationRect wanneer u wordt gelezen.                                                    |
| `gpx:cmt`                | ja     | ja     |                                                                                             |
| `gpx:copyright`          | ja     | ja     |                                                                                             |
| `gpx:desc`               | ja     | ja     | Gekopieerd naar een beschrijvingeigenschap wanneer u wordt gelezen om af te stemmen op andere XML-indelingen.               |
| `gpx:dgpsid`             | ja     | ja     |                                                                                             |
| `gpx:ele`                | ja     | ja     |                                                                                             |
| `gpx:extensions`         | Gedeeltelijke | Gedeeltelijke | Bij het lezen wordt stijlinformatie geëxtraheerd. Alle andere extensies worden afgevlakt tot een eenvoudig JSON-object. Alleen vormstijlinformatie wordt geschreven. |
| `gpx:geoidheight`        | ja     | ja     |                                                                                             |
| `gpx:gpx`                | ja     | ja     |                                                                                             |
| `gpx:hdop`               | ja     | ja     |                                                                                             |
| `gpx:link`               | ja     | ja     |                                                                                             |
| `gpx:magvar`             | ja     | ja     |                                                                                             |
| `gpx:metadata`           | ja     | ja     |                                                                                             |
| `gpx:name`               | ja     | ja     |                                                                                             |
| `gpx:pdop`               | ja     | ja     |                                                                                             |
| `gpx:rte`                | ja     | ja     |                                                                                             |
| `gpx:rtept`              | ja     | ja     |                                                                                             |
| `gpx:sat`                | ja     | ja     |                                                                                             |
| `gpx:src`                | ja     | ja     |                                                                                             |
| `gpx:sym`                | ja     | ja     | Waarde wordt vastgelegd, maar het wordt niet gebruikt om het pushpinpictogram te wijzigen.                               |
| `gpx:text`               | ja     | ja     |                                                                                             |
| `gpx:time`               | ja     | ja     |                                                                                             |
| `gpx:trk`                | ja     | ja     |                                                                                             |
| `gpx:trkpt`              | ja     | ja     |                                                                                             |
| `gpx:trkseg`             | ja     | ja     |                                                                                             |
| `gpx:type`               | ja     | ja     |                                                                                             |
| `gpx:vdop`               | ja     | ja     |                                                                                             |
| `gpx:wpt`                | ja     | ja     |                                                                                             |
| `gpx_style:color`        | ja     | ja     |                                                                                             |
| `gpx_style:line`         | Gedeeltelijke | Gedeeltelijke | `color`, `opacity` `width`, `lineCap` worden ondersteund.                                           |
| `gpx_style:opacity`      | ja     | ja     |                                                                                             |
| `gpx_style:width`        | ja     | ja     |                                                                                             |
| `gpxx:DisplayColor`      | ja     | nee      | Wordt gebruikt om de kleur van een vorm op te geven. Bij het `gpx_style:line` schrijven wordt kleur in plaats daarvan gebruikt.  |
| `gpxx:RouteExtension`    | Gedeeltelijke | nee      | Alle eigenschappen worden `properties`ingelezen in . Alleen `DisplayColor` wordt gebruikt.                     |
| `gpxx:TrackExtension`    | Gedeeltelijke | nee      | Alle eigenschappen worden `properties`ingelezen in . Alleen `DisplayColor` wordt gebruikt.                     |
| `gpxx:WaypointExtension` | Gedeeltelijke | nee      | Alle eigenschappen worden `properties`ingelezen in . Alleen `DisplayColor` wordt gebruikt.                     |
| `gpx:keywords`           | ja     | ja     |                                                                                             |
| `gpx:fix`                | ja     | ja     |                                                                                             |

#### <a name="additional-notes"></a>aanvullende opmerkingen

Bij het schrijven;

- MultiPoints worden opgesplitst in afzonderlijke waypoints.
- Polygonen en MultiPolygons worden als tracks geschreven. 
  
## <a name="supported-well-known-text-geometry-types"></a>Ondersteunde bekende tekstgeometrietypen

| Geometrietype | Lezen | Schrijven |
|--------------|:----:|:-----:|
| Punt | x | x |
| PUNT Z | x | x | 
| PUNT M | x | x<sup>[2]</sup> |
| PUNT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| LIJNTEKENREEKS | x | x |
| LINESTRING Z | x | x | 
| LIJNTEKENREEKS M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Veelhoek | x | x |
| POLYGON Z | x | x |
| VEELHOEK M | x | x<sup>[2]</sup> |
| POLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multipoint | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| TEKENREEKS MET MEERDERE REGELSNOEREN | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRIEVERZAMELING | x | x |
| GEOMETRIECOLLECTIE Z | x | x | 
| GEOMETRIECOLLECTIE M | x | x<sup>[2]</sup> | 
| GEOMETRIECOLLECTIE ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] Alleen de parameter Z wordt vastgelegd en toegevoegd als derde waarde in de waarde Positie.

\[2\] M parameter is niet vastgelegd.

## <a name="delimited-spatial-data-support"></a>Ondersteuning voor beperkte ruimtelijke gegevens

Beperkte ruimtelijke gegevens, zoals door komma's gescheiden waardebestanden (CSV), hebben vaak kolommen die ruimtelijke gegevens bevatten. Er kunnen bijvoorbeeld kolommen zijn die informatie over breedte- en lengtegraad bevatten. In de bekende tekstnotatie kan er een kolom zijn die ruimtelijke geometriegegevens bevat.

### <a name="spatial-data-column-detection"></a>Detectie van ruimtelijke gegevenskolom

Bij het lezen van een afgebakend bestand dat ruimtelijke gegevens bevat, wordt de koptekst geanalyseerd om te bepalen welke kolommen locatievelden bevatten. Als de koptekst tekstinformatie bevat, wordt deze gebruikt om de celwaarden naar het juiste type te casten. Als er geen koptekst is opgegeven, wordt de eerste rij geanalyseerd en gebruikt om een koptekst te genereren. Bij het analyseren van de eerste rij wordt een controle uitgevoerd om kolomnamen op een case-insensitive manier aan te passen aan de volgende namen. De volgorde van de namen is de prioriteit, in het geval er twee of meer namen in een bestand voorkomen.

#### <a name="latitude"></a>Breedtegraad

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Lengtegraad

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Hoogte

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Geografie

De eerste rij gegevens wordt gescand op tekenreeksen die in de bekende tekstindeling staan. 

### <a name="delimited-data-column-types"></a>Afgebakende gegevenskolomtypen

Bij het scannen van de koptekstrij worden alle tekstgegevens in de kolomnaam geëxtraheerd en gebruikt om de cellen in die kolom te casten. Hier is een voorbeeld van een kolomnaam met een tekstwaarde: "ColumnName (typeNaam)". De volgende hoofdletters worden ondersteund:

#### <a name="numbers"></a>Nummers

- edm.int64
- int
- long
- edm.double
- float
- double
- getal

#### <a name="booleans"></a>Booleans

- edm.boolean
- Booleaanse waarde
- booleaans

#### <a name="dates"></a>Datums

- edm.datetijd
- date
- datum/tijd

#### <a name="geography"></a>Geografie

- edm.geografie
- Geografie

#### <a name="strings"></a>Tekenreeksen

- edm.string
- varchar
- tekst
- tekenreeks

Als er geen type-informatie kan worden geëxtraheerd uit de koptekst en de dynamische typenoptie is ingeschakeld bij het lezen, wordt elke cel individueel geanalyseerd om te bepalen welk gegevenstype het het meest geschikt is om te worden gecast als.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten toevoegen:

> [!div class="nextstepaction"]
> [Ruimtelijke gegevens lezen en schrijven](spatial-io-read-write-spatial-data.md)
