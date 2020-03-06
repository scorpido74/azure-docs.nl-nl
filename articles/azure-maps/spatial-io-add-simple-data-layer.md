---
title: Een eenvoudige gegevenslaag toevoegen | Microsoft Azure kaarten
description: Meer informatie over hoe u een eenvoudige gegevenslaag kunt toevoegen met behulp van de ruimtelijke IO-module, die wordt verschaft door Azure Maps Web-SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3fa54e3227496c11fcafc2f42e980daa5c716365
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370984"
---
# <a name="add-a-simple-data-layer"></a>Een eenvoudige gegevenslaag toevoegen

De ruimtelijke IO-module biedt een `SimpleDataLayer` klasse. Deze klasse maakt het eenvoudig om stijl functies op de kaart weer te geven. Het kan zelfs gegevens sets weer geven met stijl eigenschappen en gegevens sets die gemengde geometrie typen bevatten. De eenvoudige gegevenslaag bereikt deze functionaliteit door meerdere weergave lagen te verpakken en stijl expressies te gebruiken. De stijl expressies zoeken naar algemene stijl eigenschappen van de functies in deze ingepakte lagen. Met de functie `atlas.io.read` en de functie `atlas.io.write` worden deze eigenschappen gebruikt om stijlen in een ondersteunde bestands indeling te lezen en te schrijven. Wanneer u de eigenschappen aan een ondersteunde bestands indeling hebt toegevoegd, kan het bestand voor verschillende doel einden worden gebruikt. Het bestand kan bijvoorbeeld worden gebruikt om de stijl functies op de kaart weer te geven.

Naast opmaak functies biedt de `SimpleDataLayer` een ingebouwde pop-upfunctie met een pop-upsjabloon. De pop-up wordt weer gegeven wanneer er op een functie wordt geklikt. De standaard pop-upfunctie kan, indien gewenst, worden uitgeschakeld. Deze laag biedt ook ondersteuning voor geclusterde gegevens. Wanneer er op een cluster wordt geklikt, wordt de kaart inzoomen op het cluster en wordt deze uitgebreid naar afzonderlijke punten en subclusteren.

De klasse `SimpleDataLayer` is bedoeld om te worden gebruikt voor grote gegevens sets met veel geometrie typen en veel stijlen die zijn toegepast op de functies. Bij gebruik van deze klasse wordt een overhead toegevoegd van zes lagen met stijl expressies. Er zijn dus gevallen waarin het efficiënter is om de kern weergave lagen te gebruiken. Gebruik bijvoorbeeld een kern laag om een aantal geometrie typen en enkele stijlen van een functie weer te geven

## <a name="default-supported-style-properties"></a>Standaard ondersteunde stijl eigenschappen

Zoals eerder vermeld, plaatst de eenvoudige gegevens laag enkele van de kern weergave lagen: bellen, symbool, lijn, veelhoek en geëxtrudeerde veelhoek. Vervolgens worden expressies gebruikt om te zoeken naar geldige stijl eigenschappen voor afzonderlijke functies.

Azure Maps-en GitHub stijl eigenschappen zijn de twee belangrijkste sets met ondersteunde eigenschapnamen. De meeste eigenschapnamen van de verschillende opties voor Azure Maps Layer worden ondersteund als stijl eigenschappen van functies in de laag Simple data. Er zijn expressies toegevoegd aan sommige laag opties om stijl eigenschaps namen te ondersteunen die vaak worden gebruikt door GitHub. Deze eigenschapnamen worden gedefinieerd door [de geojson-toewijzings ondersteuning van github](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)en worden gebruikt voor het opmaken van geojson-bestanden die worden opgeslagen en weer gegeven in het platform. Alle stijl eigenschappen van de GitHub worden ondersteund in de eenvoudige gegevenslaag, met uitzonde ring van de `marker-symbol` stijl eigenschappen.

Als de lezer een minder algemene stijl eigenschap tegen komt, wordt deze geconverteerd naar de dichtstbijzijnde Azure Maps stijl eigenschap. Daarnaast kunnen de standaard stijl expressies worden overschreven door gebruik te maken van de `getLayers` functie van de eenvoudige gegevenslaag en het bijwerken van de opties op een van de lagen.

De volgende sectie bevat informatie over de standaard stijl eigenschappen die worden ondersteund door de laag Simple data. De volg orde van de ondersteunde eigenschaps naam is ook de prioriteit van de eigenschap. Als twee stijl eigenschappen voor dezelfde laag optie zijn gedefinieerd, heeft het eerste item in de lijst een hogere prioriteit.

## <a name="simple-data-layer-options"></a>Opties voor eenvoudige gegevenslaag

### <a name="bubble-layer-style-properties"></a>Eigenschappen van de laag stijl

Als een functie een `Point` of een `MultiPoint`is en de functie geen `image`-eigenschap heeft die als een aangepast pictogram wordt gebruikt om het punt als een symbool weer te geven, wordt de functie weer gegeven met een `BubbleLayer`.

| Laag optie | Ondersteunde eigenschaps naam (en) | Standaardwaarde |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size`<sup>2</sup>, `scale`<sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] de waarden `size` en `scale` worden beschouwd als scalaire waarden en worden vermenigvuldigd met `8`

\[2\] als de optie GitHub `marker-size` is opgegeven, worden de volgende waarden gebruikt voor de RADIUS.

| Grootte van markering | RADIUS |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Clusters worden ook weer gegeven met behulp van de laag bubble. De RADIUS van een cluster is standaard ingesteld op `16`. De kleur van het cluster is afhankelijk van het aantal punten in het cluster, zoals hieronder wordt beschreven:

| aantal punten | Kleur    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>Eigenschappen van symbool stijl

Als een functie een `Point` of een `MultiPoint`is en de functie en een `image` eigenschap hebben die als een aangepast pictogram wordt gebruikt om het punt als een symbool weer te geven, wordt de functie weer gegeven met een `SymbolLayer`.

| Laag optie | Ondersteunde eigenschaps naam (en) | Standaardwaarde |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size`<sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] als de optie GitHub `marker-size` is opgegeven, worden de volgende waarden gebruikt voor de optie voor de pictogram grootte.

| Grootte van markering | Symbool grootte |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Als de punt functie een cluster is, wordt de eigenschap `point_count_abbreviated` weer gegeven als een tekst label. Er wordt geen afbeelding weer gegeven.

### <a name="line-style-properties"></a>Eigenschappen van lijn stijl

Als de functie een `LineString`, `MultiLineString`, `Polygon`of `MultiPolygon`, wordt het onderdeel gerenderd met een `LineLayer`.

| Laag optie | Ondersteunde eigenschaps naam (en) | Standaardwaarde |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Eigenschappen van polygoon stijl

Als de functie een `Polygon` of een `MultiPolygon`is en de functie geen `height`-eigenschap heeft of de eigenschap `height` is ingesteld op nul, wordt de functie met een `PolygonLayer`weer gegeven.

| Laag optie | Ondersteunde eigenschaps naam (en) | Standaardwaarde |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Eigenschappen van geëxtrudeerde polygoon stijl

Als de functie een `Polygon` of een `MultiPolygon`is en een `height`-eigenschap heeft met een waarde die groter is dan 0, wordt de functie weer gegeven met een `PolygonExtrusionLayer`.

| Laag optie | Ondersteunde eigenschaps naam (en) | Standaardwaarde |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="use-a-simple-data-layer"></a>Een eenvoudige gegevenslaag gebruiken

De klasse `SimpleDataLayer` wordt gebruikt zoals de andere rendering-lagen worden gebruikt. De volgende code laat zien hoe u een eenvoudige gegevenslaag kunt gebruiken in een kaart:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Functies toevoegen aan de gegevens bron. Vervolgens wordt met de eenvoudige gegevenslaag aangegeven hoe de functies het beste kunnen worden weer gegeven. Stijlen voor afzonderlijke functies kunnen worden ingesteld als eigenschappen voor de functie. De volgende code toont een functie van een geojson-punt met een `color` eigenschap ingesteld op `red`. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

Met de volgende code wordt de functie van het bovenstaande punt weer gegeven met behulp van de laag Simple data. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="De Simple data layer gebruiken" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Ga naar de pen en <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>Gebruik de eenvoudige gegevenslaag</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

De effectieve kracht van de eenvoudige gegevenslaag is als volgt:

- Er zijn verschillende typen functies in een gegevens bron. of
- Voor functies in de gegevensset zijn verschillende stijl eigenschappen afzonderlijk ingesteld. of
- U weet niet wat de gegevensset precies bevat.

Als u bijvoorbeeld XML-gegevensfeeds wilt parseren, weet u mogelijk niet de exacte stijlen en geometrie typen van de functies. In het volgende voor beeld ziet u de kracht van de eenvoudige gegevenslaag door de functies van een KML-bestand weer te geven. Er worden ook verschillende opties gedemonstreerd die de klasse van de eenvoudige gegevenslaag biedt.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opties voor eenvoudige gegevenslaag" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Zie de <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>eenvoudige gegevenslaag opties</a> voor Pen per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Ruimtelijke gegevens lezen en schrijven](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Een OGC-kaartLaag toevoegen](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Verbinding maken met een WFS-service](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Kern bewerkingen gebruiken](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevens indeling](spatial-io-supported-data-format-details.md)
