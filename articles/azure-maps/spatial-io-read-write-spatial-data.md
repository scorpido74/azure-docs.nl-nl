---
title: Ruimtelijke gegevens lezen en schrijven | Microsoft Azure Maps
description: Meer informatie over het lezen en schrijven van gegevens met behulp van de Spatial IO-module, geleverd door Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334158"
---
# <a name="read-and-write-spatial-data"></a>Ruimtelijke gegevens lezen en schrijven

De onderstaande tabel bevat de ruimtelijke bestandsindelingen die worden ondersteund voor lees- en schrijfbewerkingen met de module Ruimtelijk IO.

| Gegevensindeling       | Lezen | Schrijven |
|-------------------|------|-------|
| GeoJSON (GeoJSON)           | ✓  |  ✓  |
| GeoRSS (GeoRSS)            | ✓  |  ✓  |
| Gml               | ✓  |  ✓  |
| Gpx               | ✓  |  ✓  |
| Kml               | ✓  |  ✓  |
| Kmz               | ✓  |  ✓  |
| Ruimtelijke CSV       | ✓  |  ✓  |
| Bekende tekst   | ✓  |  ✓  |

In de volgende secties worden alle verschillende tools voor het lezen en schrijven van ruimtelijke gegevens beschreven met behulp van de Spatial IO module.

## <a name="read-spatial-data"></a>Ruimtelijke gegevens lezen

De `atlas.io.read` functie is de belangrijkste functie die wordt gebruikt om algemene ruimtelijke gegevensformaten zoals KML, GPX, GeoRSS, GeoJSON en CSV-bestanden met ruimtelijke gegevens te lezen. Deze functie kan ook gecomprimeerde versies van deze indelingen lezen, als een zip-bestand of een KMZ-bestand. De KMZ-bestandsindeling is een gecomprimeerde versie van KML die ook elementen zoals afbeeldingen kan bevatten. Als alternatief kan de leesfunctie een URL innemen die naar een bestand in een van deze indelingen verwijst. URL's moeten worden gehost op een cors-eindpunt of er moet een proxyservice worden aangeboden in de leesopties. De proxyservice wordt gebruikt om resources te laden op domeinen die niet zijn ingeschakeld voor CORS. De leesfunctie retourneert een belofte om de afbeeldingspictogrammen aan de kaart toe te voegen en verwerkt gegevens asynchroon om de impact op de ui-thread te minimaliseren.

Bij het lezen van een gecomprimeerd bestand, als een zip of een KMZ, wordt het uitgepakt en gescand voor het eerste geldige bestand. Bijvoorbeeld doc.kml of een bestand met een andere geldige extensie, zoals: .kml, .xml, geojson, .json, .csv, .tsv of .txt. Vervolgens worden afbeeldingen waarnaar wordt verwezen in KML- en GeoRSS-bestanden vooraf geladen om ervoor te zorgen dat ze toegankelijk zijn. Ontoegankelijke afbeeldingsgegevens kunnen een alternatieve terugvalafbeelding laden of uit de stijlen worden verwijderd. Afbeeldingen die uit KMZ-bestanden worden geëxtraheerd, worden geconverteerd naar gegevens-URI's.

Het resultaat van de `SpatialDataSet` leesfunctie is een object. Met dit object wordt de klasse GeoJSON FeatureCollection uitgebreid. Het kan gemakkelijk worden `DataSource` doorgegeven in een as-is om de functies weer te geven op een kaart. De `SpatialDataSet` functie-informatie bevat niet alleen functie-informatie, maar het kan ook KML-grondoverlays, verwerkingsstatistieken en andere details bevatten, zoals beschreven in de volgende tabel.

| Naam van eigenschap | Type | Beschrijving | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Selectiekader met alle gegevens in de gegevensset. |
| `features` | `Feature[]` | GeoJSON beschikt over functies binnen de gegevensset. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Een array van KML GroundOverlays. |
| `icons` | Tekenreeks,&lt;tekenreeks&gt; | Een set pictogram-URL's. Sleutel = pictogramnaam, Waarde = URL. |
| properties | elke | Eigendomsinformatie op documentniveau van een ruimtelijke gegevensset. |
| `stats` | `SpatialDataSetStats` | Statistieken over de inhoud en verwerkingstijd van een ruimtelijke gegevensset. |
| `type` | `'FeatureCollection'` | Alleen-lezen GeoJSON-typewaarde. |

## <a name="examples-of-reading-spatial-data"></a>Voorbeelden van het lezen van ruimtelijke gegevens

De volgende code laat zien hoe u een ruimtelijke gegevensset `SimpleDataLayer` leest en deze op de kaart weergeeft met behulp van de klasse. De code maakt gebruik van een GPX-bestand waarnaar wordt gewezen door een URL.

<br/>

<iframe height='500' scrolling='no' title='Eenvoudige ruimtelijke gegevens laden' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen Load Spatial Data<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>Simple</a> by Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

De volgende code demo laat zien hoe te lezen en laden KML, of KMZ, op de kaart. KML kan grondoverlays bevatten, die in `ImageLyaer` de `OgcMapLayer`vorm van een of . Deze overlays moeten los van de functies op de kaart worden toegevoegd. Bovendien, als de gegevensset aangepaste pictogrammen bevat, moeten deze pictogrammen worden geladen op de kaartenbronnen voordat de functies worden geladen.

<br/>

<iframe height='500' scrolling='no' title='KML laden op kaart' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>Load KML Onto Map by</a> Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

U optioneel een proxyservice bieden voor toegang tot cross-domeinassets waarvoor CORS mogelijk niet is ingeschakeld. De leesfunctie probeert eerst bestanden op een ander domein te openen met CORS. Na de eerste keer dat het geen toegang heeft tot een bron op een ander domein met CORS, wordt alleen aanvullende bestanden aangevraagd als er een proxyservice is geleverd. De leesfunctie voegt de URL van het bestand toe aan het einde van de opgegeven proxy-URL. Dit codefragment laat zien hoe u een proxyservice doorgeeft aan de leesfunctie:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

De demo hieronder laat zien hoe je een afgebakend bestand te lezen en te maken op de kaart. In dit geval maakt de code gebruik van een CSV-bestand met ruimtelijke gegevenskolommen.

<br/>

<iframe height='500' scrolling='no' title='Een afgebakend bestand toevoegen' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>Een afgebakend bestand toevoegen</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Ruimtelijke gegevens schrijven

Er zijn twee belangrijke schrijffuncties in de ruimtelijke IO-module. De `atlas.io.write` functie genereert een `atlas.io.writeCompressed` tekenreeks, terwijl de functie een gecomprimeerd zip-bestand genereert. Het gecomprimeerde zip-bestand zou een op tekst gebaseerd bestand bevatten met de ruimtelijke gegevens erin. Beide functies geven een belofte om de gegevens aan het bestand toe te voegen. En ze kunnen beide een van `SpatialDataSet`de `DataSource` `ImageLayer`volgende `OgcMapLayer`gegevens schrijven: , , , functieverzameling, functie, geometrie of een array van een combinatie van deze gegevenstypen. Wanneer u met beide functies schrijft, u de gewenste bestandsindeling opgeven. Als de bestandsindeling niet is opgegeven, worden de gegevens als KML geschreven.

De tool hieronder toont de meerderheid van de schrijfopties die kunnen worden gebruikt met de `atlas.io.write` functie.

<br/>

<iframe height='700' scrolling='no' title='Opties voor het schrijven van ruimtelijke gegevens' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>opties voor het schrijven van penruimtelijke gegevens</a> door Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Voorbeeld van het schrijven van ruimtelijke gegevens

Met het volgende voorbeeld u ruimtelijke bestanden op de kaart slepen en neerzetten en vervolgens laden. U GeoJSON-gegevens exporteren vanaf de kaart en deze schrijven in een van de ondersteunde ruimtelijke gegevensindelingen als tekenreeks of als gecomprimeerd bestand.

<br/>

<iframe height='700' scrolling='no' title='Ruimtelijke bestanden slepen en neerzetten naar kaart' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>pensleep- en neerzetruimtelijke bestanden op map</a> by Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

U optioneel een proxyservice bieden voor toegang tot cross-domeinassets waarvoor CORS mogelijk niet is ingeschakeld. Dit fragment van code laat zien dat u een proxyservice opnemen:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Lees en schrijf Bekende Tekst (WKT)

[Bekende tekst](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) is een Open Geospatial Consortium (OGC) standaard voor het weergeven van ruimtelijke geometrieën als tekst. Veel georuimtelijke systemen ondersteunen WKT, zoals Azure SQL en Azure PostgreSQL met behulp van de PostGIS-plug-in. Zoals de meeste OGC-normen worden coördinaten opgemaakt als "lengtebreedte" om af te stemmen op de "x y"-conventie. Als voorbeeld, een punt op lengte -110 en breedtegraad `POINT(-110 45)` 45 kan worden geschreven als met behulp van de WKT-formaat.

Bekende tekst kan worden gelezen `atlas.io.ogc.WKT.read` met behulp van `atlas.io.ogc.WKT.write` de functie, en geschreven met behulp van de functie.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Voorbeelden van lezen en schrijven van bekende tekst (WKT)

De volgende code laat zien hoe u `POINT(-122.34009 47.60995)` de bekende teksttekenreeks lezen en op de kaart renderen met behulp van een bellenlaag.

<br/>

<iframe height='500' scrolling='no' title='Lees bekende tekst' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>lees bekende tekst</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>van Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

De volgende code toont het lezen en schrijven van bekende tekst heen en weer.

<br/>

<iframe height='700' scrolling='no' title='Bekende tekst lezen en schrijven' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>lees en schrijf bekende</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>tekst van Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>GML lezen en schrijven

GML is een ruimtelijke XML-bestandsspecificatie die vaak wordt gebruikt als een uitbreiding naar andere XML-specificaties. GeoJSON-gegevens kunnen met behulp van de `atlas.io.core.GmlWriter.write` functie als XML worden geschreven met GML-tags. De XML die GML bevat, `atlas.io.core.GmlReader.read` kan worden gelezen met behulp van de functie. De leesfunctie heeft twee opties:

- De `isAxisOrderLonLat` optie - De asvolgorde van coördinaten "breedte, lengte" of "lengtegraad, breedtegraad" kan variëren tussen gegevenssets en is niet altijd goed gedefinieerd. Standaard leest de GML-lezer de coördinatengegevens als "breedtegraad, lengtegraad", maar als u deze optie op true instelt, wordt deze gelezen als "lengtegraad, breedtegraad".
- De `propertyTypes` optie - Deze optie is een opzoektabel met de sleutelwaarde waarbij de sleutel de naam is van een eigenschap in de gegevensset. De waarde is het objecttype waarnaar moet worden gegoten bij het ontwenen. De ondersteunde typewaarden `string`zijn: `boolean`, `date` `number`, en . Als een eigenschap zich niet in de opzoektabel bevindt of als het type niet is gedefinieerd, wordt de eigenschap ontleed als een tekenreeks.

De `atlas.io.read` functie wordt `atlas.io.core.GmlReader.read` standaard ingesteld op de functie wanneer wordt gedetecteerd dat de invoergegevens XML zijn, maar de gegevens zijn niet een van de andere ondersteunende ruimtelijke XML-indelingen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [atlas.io statische functies](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.io.ogc.WKT functies](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten toevoegen:

> [!div class="nextstepaction"]
> [Een OGC-kaartlaag toevoegen](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Verbinding maken met een WFS-service](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Benut kernactiviteiten](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevensindeling](spatial-io-supported-data-format-details.md)
