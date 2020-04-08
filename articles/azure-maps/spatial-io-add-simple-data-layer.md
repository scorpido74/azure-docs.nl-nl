---
title: Voeg een eenvoudige gegevenslaag toe | Microsoft Azure Maps
description: Meer informatie over het toevoegen van een eenvoudige gegevenslaag met behulp van de Spatial IO-module, geleverd door Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8862c33b7660b8130f692dc4beea89a7b6b5f5ad
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804483"
---
# <a name="add-a-simple-data-layer"></a>Een eenvoudige gegevenslaag toevoegen

De ruimtelijke IO-module biedt een `SimpleDataLayer` klasse. Deze klasse maakt het gemakkelijk om gestileerde functies op de kaart weer te geven. Het kan zelfs gegevenssets renderen met stijleigenschappen en gegevenssets die gemengde geometrietypen bevatten. De eenvoudige gegevenslaag bereikt deze functionaliteit door meerdere renderinglagen te verpakken en stijlexpressies te gebruiken. De stijlexpressies zoeken naar algemene stijleigenschappen van de functies in deze verpakte lagen. De `atlas.io.read` functie `atlas.io.write` en de functie gebruiken deze eigenschappen om stijlen te lezen en te schrijven in een ondersteunde bestandsindeling. Nadat u de eigenschappen aan een ondersteunde bestandsindeling hebt toegevoegd, kan het bestand voor verschillende doeleinden worden gebruikt. Het bestand kan bijvoorbeeld worden gebruikt om de gestileerde functies op de kaart weer te geven.

Naast stylingfuncties biedt `SimpleDataLayer` de functie een ingebouwde pop-upfunctie met een pop-upsjabloon. De pop-up wordt weergegeven wanneer op een functie wordt geklikt. De standaardpopupfunctie kan desgewenst worden uitgeschakeld. Deze laag ondersteunt ook geclusterde gegevens. Wanneer op een cluster wordt geklikt, zoomt de kaart in op het cluster en breidt het uit in afzonderlijke punten en subclusters.

De `SimpleDataLayer` klasse is bedoeld om te worden gebruikt op grote gegevenssets met veel geometrietypen en veel stijlen die op de functies zijn toegepast. Bij gebruik voegt deze klasse een overhead toe van zes lagen met stijlexpressies. Er zijn dus gevallen waarin het efficiënter is om de basisrenderlagen te gebruiken. Gebruik bijvoorbeeld een kernlaag om een paar geometrietypen en een paar stijlen op een functie weer te geven

## <a name="use-a-simple-data-layer"></a>Een eenvoudige gegevenslaag gebruiken

De `SimpleDataLayer` klasse wordt gebruikt zoals de andere renderinglagen worden gebruikt. De onderstaande code laat zien hoe u een eenvoudige gegevenslaag in een kaart gebruiken:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Functies toevoegen aan de gegevensbron. Vervolgens zal de eenvoudige gegevenslaag erachter komen hoe de functies het beste kunnen worden weergegeven. Stijlen voor afzonderlijke functies kunnen worden ingesteld als eigenschappen op de functie. De volgende code toont een GeoJSON-puntfunctie met een `color` eigenschap die is ingesteld op `red`. 

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

Met de volgende code wordt de bovenstaande puntfunctie weergegeven met behulp van de eenvoudige gegevenslaag. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="De laag Eenvoudige gegevens gebruiken" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true"> Zie de pen Gebruik de eenvoudige<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>gegevenslaag</a> van Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

De echte kracht van de eenvoudige gegevenslaag komt wanneer:

- Er zijn verschillende soorten functies in een gegevensbron; Of
- Functies in de gegevensset hebben verschillende stijleigenschappen die afzonderlijk zijn ingesteld. Of
- U weet niet precies wat de gegevensset bevat.

Wanneer u bijvoorbeeld XML-gegevensfeeds ontweitt, weet u mogelijk niet de exacte stijlen en geometrietypen van de functies. In het volgende voorbeeld ziet u de kracht van de eenvoudige gegevenslaag door de functies van een KML-bestand weer te geven. Het toont ook verschillende opties die de eenvoudige gegevenslaagklasse biedt.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Eenvoudige opties voor gegevenslagen" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Zie de <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>opties voor peneenvoudige gegevenslaag</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Deze eenvoudige gegevenslaag gebruikt de [categorie pop-upsjabloon](map-add-popup.md#add-popup-templates-to-the-map) om KML-ballonnen weer te geven of eigenschappen als tabel weer te geven. Standaard wordt alle inhoud die in de pop-up wordt weergegeven, sandboxed in een iframe als beveiligingsfunctie. Er zijn echter beperkingen:
>
> - Alle scripts, formulieren, aanwijzervergrendeling en de bovenste navigatiefunctionaliteit zijn uitgeschakeld. Koppelingen mogen worden geopend in een nieuw tabblad wanneer er op wordt geklikt. 
> - Oudere browsers die de `srcdoc` parameter op iframes niet ondersteunen, worden beperkt tot het renderen van een kleine hoeveelheid inhoud.
> 
> Als u erop vertrouwt dat de gegevens die in de pop-ups worden geladen en deze scripts mogelijk in pop-ups worden geladen, toegang krijgen tot uw toepassing, u dit uitschakelen door de optie pop-upsjablonen `sandboxContent` in te stellen op false. 

## <a name="default-supported-style-properties"></a>Standaardondersteunde stijleigenschappen

Zoals eerder vermeld, de eenvoudige gegevens laag wraps een aantal van de kern rendering lagen: bubble, symbool, lijn, veelhoek, en geëxtrudeerd veelhoek. Vervolgens worden expressies gebruikt om te zoeken naar geldige stijleigenschappen voor afzonderlijke functies.

Azure Maps- en GitHub-stijleigenschappen zijn de twee belangrijkste sets ondersteunde eigenschapsnamen. De meeste eigenschapsnamen van de verschillende azure maps-laagopties worden ondersteund als stijleigenschappen van functies in de eenvoudige gegevenslaag. Expressies zijn toegevoegd aan een aantal laagopties ter ondersteuning van eigenschapsnamen die vaak door GitHub worden gebruikt. Deze eigenschapsnamen worden gedefinieerd door [de GeoJSON-kaartondersteuning van GitHub](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)en worden gebruikt om GeoJSON-bestanden te stylen die binnen het platform zijn opgeslagen en gerenderd. Alle stylingeigenschappen van GitHub worden ondersteund in de eenvoudige `marker-symbol` gegevenslaag, behalve de stylingeigenschappen.

Als de lezer een minder algemene eigenschap van stijl tegenkomt, wordt deze geconverteerd naar de dichtstbijzijnde eigenschap Azure Maps-stijl. Bovendien kunnen de standaardstijlexpressies worden overschreven `getLayers` door de functie van de eenvoudige gegevenslaag te gebruiken en de opties op een van de lagen bij te werken.

In de volgende secties vindt u informatie over de standaardstijleigenschappen die worden ondersteund door de eenvoudige gegevenslaag. De volgorde van de ondersteunde eigenschapnaam is ook de prioriteit van het pand. Als twee stijleigenschappen zijn gedefinieerd voor dezelfde laagoptie, heeft de eerste in de lijst een hogere prioriteit.

### <a name="bubble-layer-style-properties"></a>Stijleigenschappen van bellenlaag

Als een functie `Point` een `MultiPoint`of een , en `image` de functie heeft geen eigenschap die zou worden gebruikt als een aangepast pictogram `BubbleLayer`om het punt weer te geven als een symbool, dan is de functie zal worden weergegeven met een .

| Laag, optie | Naam(en) van ondersteunde eigenschap | Standaardwaarde |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup> `marker-size` <sup>2</sup>, `scale`2 , <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size` De `scale` waarden en waarden worden beschouwd als scalaire waarden en worden vermenigvuldigd met`8`

\[2\] Als de `marker-size` GitHub-optie is opgegeven, worden de volgende waarden gebruikt voor de straal.

| Grootte van markering | Radius |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Clusters worden ook weergegeven met behulp van de bellenlaag. Standaard is de straal van `16`een cluster ingesteld op . De kleur van het cluster is afhankelijk van het aantal punten in het cluster, zoals hieronder gedefinieerd:

| Aantal punten | Kleur    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt;10     | `green`  |

### <a name="symbol-style-properties"></a>Eigenschappen van symboolstijl

Als een functie `Point` een `MultiPoint`of een , `image` en de functie is en een eigenschap heeft die zou worden gebruikt als `SymbolLayer`een aangepast pictogram om het punt als symbool weer te geven, wordt de functie weergegeven met een .

| Laag, optie | Naam(en) van ondersteunde eigenschap | Standaardwaarde |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1.</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] Als de `marker-size` optie GitHub is opgegeven, worden de volgende waarden gebruikt voor de optie pictogramgrootte.

| Grootte van markering | Grootte van symbool |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Als de puntfunctie een `point_count_abbreviated` cluster is, wordt de eigenschap weergegeven als een tekstlabel. Er wordt geen afbeelding weergegeven.

### <a name="line-style-properties"></a>Eigenschappen lijnstijl

Als de functie `LineString` `MultiLineString`een `Polygon`, `MultiPolygon`, , of , dan `LineLayer`is de functie zal worden weergegeven met een .

| Laag, optie | Naam(en) van ondersteunde eigenschap | Standaardwaarde |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Polygoonstijleigenschappen

Als de functie `Polygon` een `MultiPolygon`of een is en de `height` functie `height` geen eigenschap heeft of als de eigenschap `PolygonLayer`nul is, wordt de functie weergegeven met een .

| Laag, optie | Naam(en) van ondersteunde eigenschap | Standaardwaarde |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Geëxtrudeerde polygoonstijleigenschappen

Als de functie `Polygon` een `MultiPolygon`of een `height` , en heeft een eigenschap met een waarde `PolygonExtrusionLayer`groter dan 0, zal de functie worden weergegeven met een .

| Laag, optie | Naam(en) van ondersteunde eigenschap | Standaardwaarde |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [SimpleDataLayer SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten toevoegen:

> [!div class="nextstepaction"]
> [Ruimtelijke gegevens lezen en schrijven](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Een OGC-kaartlaag toevoegen](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Verbinding maken met een WFS-service](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Benut kernactiviteiten](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevensindeling](spatial-io-supported-data-format-details.md)
