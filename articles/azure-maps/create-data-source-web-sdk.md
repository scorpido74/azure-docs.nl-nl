---
title: Een gegevens bron maken voor een kaart | Microsoft Azure kaarten
description: In dit artikel leert u hoe u een gegevens bron kunt maken en toevoegen aan een kaart met behulp van de Web-SDK van Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77190231"
---
# <a name="create-a-data-source"></a>Een gegevensbron maken

De Azure Maps Web-SDK slaat gegevens op in gegevens bronnen. Het gebruik van gegevens bronnen optimaliseert de gegevens bewerkingen voor het uitvoeren van query's en rendering. Er zijn momenteel twee soorten gegevens bronnen:

**Geojson-gegevens bron**

Met een geojson gebaseerde gegevens bron worden gegevens lokaal geladen en opgeslagen `DataSource` met behulp van de-klasse. Geojson-gegevens kunnen hand matig worden gemaakt of gemaakt met behulp van de Help-klassen in de naam ruimte [Atlas. data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . De `DataSource` klasse biedt functies voor het importeren van lokale of externe geojson-bestanden. Externe geojson-bestanden moeten worden gehost op een eind punt waarvoor CORs is ingeschakeld. De `DataSource` klasse biedt functionaliteit voor cluster-punt gegevens. En kunnen gegevens eenvoudig worden toegevoegd, verwijderd en bijgewerkt met de `DataSource` -klasse.


> [!TIP]
> Stel dat u wilt dat alle gegevens in een `DataSource`worden overschreven. Als u aanroepen naar de `clear` then `add` -functies, kan de kaart twee keer opnieuw worden weer gegeven. Dit kan een beetje vertraging veroorzaken. Gebruik in plaats `setShapes` daarvan de functie, waarmee alle gegevens in de gegevens bron worden verwijderd en vervangen en slechts één weer gave van de kaart wordt geactiveerd.

**Bron van vector tegel**

Een vector tegel bron beschrijft hoe een vector tegel laag kan worden geopend. Gebruik de [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) -klasse om een vector tegel bron te instantiëren. Vector tegel lagen zijn vergelijkbaar met de tegel lagen, maar ze zijn niet hetzelfde. Een tegel laag is een raster afbeelding. Vector tegel lagen zijn een gecomprimeerd bestand in de PBF-indeling. Dit gecomprimeerde bestand bevat gegevens van een vector kaart en een of meer lagen. Het bestand kan worden gerenderd en opgemaakt op de client, op basis van de stijl van elke laag. De gegevens in een vector tegel bevatten geografische functies in de vorm van punten, lijnen en veelhoeken. Er zijn verschillende voor delen van het gebruik van vector tegel lagen in plaats van raster tegel lagen:

 - Een bestands grootte van een vector tegel is doorgaans veel kleiner dan een gelijkwaardige raster tegel. Als zodanig wordt er minder band breedte gebruikt. Dit betekent een lagere latentie, een snellere kaart en een betere gebruikers ervaring.
 - Omdat vector tegels worden weer gegeven op de client, worden ze aangepast aan de resolutie van het apparaat waarop ze worden weer gegeven. Als gevolg hiervan worden de gerenderde kaarten meer duidelijk gedefinieerd, met kristalheldere labels.
 - Voor het wijzigen van de stijl van de gegevens in de vector kaarten hoeven de gegevens niet opnieuw te worden gedownload omdat de nieuwe stijl op de client kan worden toegepast. Als u daarentegen de stijl van een raster tegel laag wijzigt, moet meestal het laden van tegels van de server worden geladen en vervolgens de nieuwe stijl wordt toegepast.
 - Omdat de gegevens in een vector vorm worden geleverd, is er minder server-side verwerking vereist om de gegevens voor te bereiden. Als gevolg hiervan kunnen de nieuwere gegevens sneller beschikbaar worden gemaakt.

Alle lagen die gebruikmaken van een vector bron moeten een `sourceLayer` waarde opgeven.

Zodra u deze hebt gemaakt, kunt u gegevens bronnen toevoegen aan de `map.sources` kaart via de eigenschap. Dit is een [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). De volgende code laat zien hoe u een `DataSource` maakt en hoe u deze toevoegt aan de kaart.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure Maps voldoet aan de [tegel specificatie Mapbox vector](https://github.com/mapbox/vector-tile-spec), een open standaard.

## <a name="connecting-a-data-source-to-a-layer"></a>Een gegevens bron verbinden met een laag

De gegevens worden weer gegeven op de kaart met behulp van lagen renderen. Naar één gegevens bron kan worden verwezen door een of meer rendering-lagen. Voor de volgende weergave lagen is een gegevens bron vereist:

- [Bubble Layer](map-add-bubble-layer.md) : geeft punt gegevens weer als geschaalde cirkels op de kaart.
- [Symbol-laag](map-add-pin.md) : geeft punt gegevens weer als pictogrammen of tekst.
- [Heatmap voor hitte](map-add-heat-map-layer.md) : geeft punt gegevens weer als een hitte heatmap.
- [Line Layer](map-add-shape.md) : een lijn weer geven en het overzicht van veelhoeken weer geven. 
- [Veelhoek-laag](map-add-shape.md) : Hiermee wordt het gebied van een veelhoek gevuld met een effen kleur of een patroon voor een afbeelding.

De volgende code laat zien hoe u een gegevens bron maakt, hoe u deze toevoegt aan de kaart en hoe u deze verbindt met een Bubble Layer. En importeer vervolgens geojson Point-gegevens vanaf een externe locatie naar de gegevens bron. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Er zijn extra rendering lagen die geen verbinding maken met deze gegevens bronnen, maar ze kunnen de gegevens rechtstreeks laden voor rendering. 

- [Afbeelding slaag](map-add-image-layer.md) : bedekking één afbeelding boven op de kaart en koppelt de hoeken aan een set opgegeven coördinaten.
- [Tegel laag](map-add-tile-layer.md) : er wordt een raster tegel laag boven op de kaart opgelegd.

## <a name="one-data-source-with-multiple-layers"></a>Eén gegevens bron met meerdere lagen

Meerdere lagen kunnen worden verbonden met één gegevens bron. Er zijn veel verschillende scenario's waarin deze optie handig is. Denk bijvoorbeeld na over het scenario waarin een gebruiker een veelhoek tekent. Het veelhoek gebied moet worden weer gegeven en gevuld wanneer de gebruiker punten toevoegt aan de kaart. Als u een lijn met stijl hebt toegevoegd om het overzicht van de veelhoek te maken, kunt u de randen van de veelhoek gemakkelijker zien, omdat de gebruiker tekent. Voor een gemakkelijke bewerking van een afzonderlijke positie in de veelhoek, kunnen we boven elke positie een koppeling toevoegen, zoals een pincode of een markering.

![Toewijzing met meerdere lagen die gegevens uit een enkele gegevens bron weer geven](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

In de meeste toewijzings platforms hebt u een veelhoek object, een lijn object en een pincode voor elke positie in de veelhoek nodig. Als de veelhoek is gewijzigd, moet u de regel en pincodes hand matig bijwerken. Dit kan snel complexer worden.

Met Azure Maps hoeft u alleen maar één veelhoek in een gegevens bron te hebben, zoals in de onderstaande code wordt weer gegeven.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Bron](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een pop-up toevoegen](map-add-popup.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"]
> [Een bubbellaag toevoegen](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)

> [!div class="nextstepaction"]
> [Een heatmap toevoegen](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)