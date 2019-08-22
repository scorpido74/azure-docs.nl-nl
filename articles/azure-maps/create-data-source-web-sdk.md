---
title: Een gegevens bron maken in Azure Maps | Microsoft Docs
description: Een gegevens bron maken en deze gebruiken met Azure Maps Web-SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 36c06182d0807ce3d255477a865023ae7b74e2cb
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874931"
---
# <a name="create-a-data-source"></a>Een gegevensbron maken

De Azure Maps Web-SDK slaat gegevens op in gegevens bronnen waarmee de gegevens voor het uitvoeren van query's en rendering worden geoptimaliseerd. Er zijn momenteel twee soorten gegevens bronnen:

**Geojson-gegevens bron**

Met een geojson-gegevens bron kunnen gegevens lokaal worden geladen en opgeslagen `DataSource` met behulp van de-klasse. Geojson-gegevens kunnen hand matig worden gemaakt of gemaakt met behulp van de Help-klassen in de naam ruimte [Atlas. data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . De `DataSource` klasse biedt functies voor het importeren van lokale of externe geojson-bestanden. Externe geojson-bestanden moeten worden gehost op een eind punt waarvoor CORs is ingeschakeld. De `DataSource` klasse biedt functionaliteit voor cluster-punt gegevens. Gegevens kunnen eenvoudig worden toegevoegd, verwijderd en bijgewerkt met de `DataSource` -klasse.


> [!TIP]
> Als u alle gegevens in een `DataSource`wilt overschrijven, kunt u, als u `add` de `clear` functies aanroept, de kaart twee keer opnieuw renderen, wat een enigszins vertraging kan veroorzaken. Gebruik in plaats `setShapes` daarvan de functie waarmee alle gegevens in de gegevens bron worden verwijderd en vervangen en slechts één weer gave van de kaart wordt geactiveerd.

**Bron van vector tegel**

Een vector tegel bron beschrijft hoe een vector tegel laag kan worden geopend en kan worden gemaakt met behulp van de [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) -klasse. Azure Maps wordt uitgelijnd met de [Mapbox-specificatie van de vector tegel](https://github.com/mapbox/vector-tile-spec). Dit is een open-standaard. Vector tegel lagen zijn vergelijkbaar met tegel lagen, maar in plaats van elke tegel een raster afbeelding te zijn, zijn ze een gecomprimeerd bestand (PBF-indeling) met gegevens over vector kaarten en een of meer lagen die op de client kunnen worden gerenderd en opgemaakt op basis van de stijl van elke laag. De gegevens in een vector tegel bevatten geografische functies in de vorm van punten, lijnen en veelhoeken. Er zijn verschillende voor delen van vector tegel lagen over raster tegel lagen.

 - Een bestands grootte van een vector tegel is doorgaans veel kleiner dan een gelijkwaardige raster tegel. Als zodanig wordt er minder band breedte gebruikt, wat betekent dat lagere latentie en een snellere kaart. Hiermee maakt u een betere gebruikers ervaring.
 - Omdat vector tegels worden weer gegeven op de client, kunnen ze worden aangepast aan de resolutie van het apparaat waarop ze worden weer gegeven. Dit maakt het mogelijk dat de gerenderde kaarten veel meer duidelijker worden gedefinieerd en met kristalheldere labels. 
 - Het wijzigen van de stijl van de gegevens in de vector kaarten vereist niet dat de gegevens opnieuw worden gedownload omdat de nieuwe stijl op de client kan worden toegepast. Als u daarentegen de stijl van een raster tegel laag wilt wijzigen, moet meestal het laden van tegels van de server waarop de nieuwe stijl wordt toegepast, worden geladen.
 - Omdat de gegevens in een vector vorm worden geleverd, is er minder server-side verwerking vereist om de gegevens voor te bereiden, wat betekent dat nieuwere gegevens sneller beschikbaar kunnen worden gemaakt.

Alle lagen die gebruikmaken van een vector bron moeten een `sourceLayer` waarde opgeven. 

Zodra u deze hebt gemaakt, kunt u gegevens bronnen toevoegen aan de `map.sources` kaart via de eigenschap. Dit is een [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). De volgende code laat zien hoe u een `DataSource` maakt en hoe u deze toevoegt aan de kaart.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>Een gegevens bron verbinden met een laag

De gegevens worden weer gegeven op de kaart met behulp van lagen renderen. Naar één gegevens bron kan worden verwezen door een of meer rendering-lagen. Voor de volgende weergave lagen moet een gegevens bron worden uitgeschakeld:

- [Bubble Layer](map-add-bubble-layer.md) : geeft punt gegevens weer als geschaalde cirkels op de kaart.
- [Symbol-laag](map-add-pin.md) : geeft punt gegevens weer als pictogrammen en/of tekst.
- [Heatmap voor hitte](map-add-heat-map-layer.md) : geeft punt gegevens weer als een hitte heatmap.
- [Lijn-laag](map-add-shape.md) : kan worden gebruikt om de lijn en of het overzicht van veelhoeken weer te geven. 
- [Veelhoek-laag](map-add-shape.md) : Hiermee wordt het gebied van een veelhoek gevuld met een effen kleur of een patroon voor een afbeelding.

De volgende code laat zien hoe u een gegevens bron maakt, deze toevoegt aan de kaart en deze verbindt met een tekenlaag en vervolgens geojson Point-gegevens vanaf een externe locatie naar de laag kunt importeren. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Er zijn extra rendering lagen die geen verbinding maken met deze gegevens bronnen, maar de gegevens die ze rechtstreeks renderen, worden geladen. 

- [Afbeelding slaag](map-add-image-layer.md) : bedekking één afbeelding boven op de kaart en koppelt de hoeken aan een set opgegeven coördinaten.
- [Tegel laag](map-add-tile-layer.md) : er wordt een raster tegel laag boven op de kaart opgelegd.

## <a name="one-data-source-with-multiple-layers"></a>Eén gegevens bron met meerdere lagen

Meerdere lagen kunnen worden verbonden met één gegevens bron. Dit kan oneven klinken, maar er zijn veel verschillende scenario's waarin dit nuttig wordt. Neem bijvoorbeeld het scenario voor het maken van een veelhoek tekening. Wanneer een gebruiker een veelhoek tekent, moeten we het opvul gebied voor de opvulling renderen, omdat de gebruiker punten aan de kaart toevoegt. Als u een lijn met stijl hebt toegevoegd die de veelhoek omlijnt, ziet u de randen van de veelhoek terwijl deze worden getekend. Als u een bepaalde Sorteer bewerking, zoals een pincode of markering, toevoegt, wordt het gemakkelijker om elke afzonderlijke positie te bewerken. Hier volgt een afbeelding waarin dit scenario wordt gedemonstreerd.

![Toewijzing met meerdere lagen die gegevens uit een enkele gegevens bron weer geven](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Als u dit scenario in de meeste toewijzings platforms wilt uitvoeren, moet u een veelhoek object, een lijn object en een pincode voor elke positie in de veelhoek maken. Als de veelhoek is gewijzigd, moet u de regel en pincodes hand matig bijwerken. deze kunnen snel complexer worden.

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
> [Een pop-upvenster toevoegen](map-add-popup.md)

> [!div class="nextstepaction"]
> [Op gegevens gebaseerde stijl expressies gebruiken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Een symbool laag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Een line laag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoon laag toevoegen](map-add-shape.md)

> [!div class="nextstepaction"]
> [Een heatmap toevoegen](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)