---
title: Een gegevensbron voor een kaart maken | Microsoft Azure Maps
description: In dit artikel leert u hoe u een gegevensbron maakt en deze aan een kaart toevoegt met de Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190231"
---
# <a name="create-a-data-source"></a>Een gegevensbron maken

De Azure Maps Web SDK slaat gegevens op in gegevensbronnen. Het gebruik van gegevensbronnen optimaliseert de gegevensbewerkingen voor query's en rendering. Momenteel zijn er twee soorten gegevensbronnen:

**GeoJSON-gegevensbron**

Een op GeoJSON gebaseerde gegevensbron laadt `DataSource` en slaat gegevens lokaal op met behulp van de klasse. GeoJSON-gegevens kunnen handmatig worden gemaakt of gemaakt met behulp van de helperklassen in de naamruimte [atlas.data.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) De `DataSource` klasse biedt functies voor het importeren van lokale of externe GeoJSON-bestanden. Externe GeoJSON-bestanden moeten worden gehost op een eindpunt met CORs. De `DataSource` klasse biedt functionaliteit voor het clusteren van puntgegevens. En gegevens kunnen eenvoudig worden toegevoegd, verwijderd `DataSource` en bijgewerkt met de klasse.


> [!TIP]
> Laten we zeggen dat u alle `DataSource`gegevens wilt overschrijven in een . Als u naar `clear` de `add` dansfuncties belt, kan de kaart twee keer opnieuw renderen, wat een beetje vertraging kan veroorzaken. Gebruik in `setShapes` plaats daarvan de functie, die alle gegevens in de gegevensbron verwijdert en vervangt en slechts één re-render van de kaart activeert.

**Vectortegelbron**

Een vectortegelbron beschrijft hoe u toegang krijgt tot een vectortegellaag. Gebruik de klasse [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) om een vectortegelbron te instantiëren. Vectortegellagen zijn vergelijkbaar met tegellagen, maar ze zijn niet hetzelfde. Een tegellaag is een rasterafbeelding. Vectortegellagen zijn een gecomprimeerd bestand in PBF-indeling. Dit gecomprimeerde bestand bevat vectorkaartgegevens en een of meer lagen. Het bestand kan worden weergegeven en gestyled op de client, op basis van de stijl van elke laag. De gegevens in een vectortegel bevatten geografische kenmerken in de vorm van punten, lijnen en veelhoeken. Er zijn verschillende voordelen van het gebruik van vectortegellagen in plaats van rastertegellagen:

 - Een bestandsgrootte van een vectortegel is doorgaans veel kleiner dan een gelijkwaardige rastertegel. Als zodanig wordt minder bandbreedte gebruikt. Het betekent een lagere latentie, een snellere kaart en een betere gebruikerservaring.
 - Aangezien vectortegels op de client worden weergegeven, passen ze zich aan de resolutie aan van het apparaat waarop ze worden weergegeven. Als gevolg hiervan lijken de gerenderde kaarten beter gedefinieerd, met kristalheldere labels.
 - Als u de stijl van de gegevens in de vectorkaarten wijzigt, hoeft u de gegevens niet opnieuw te downloaden, omdat de nieuwe stijl op de client kan worden toegepast. Het wijzigen van de stijl van een rastertegellaag vereist daarentegen meestal het laden van tegels van de server en het toepassen van de nieuwe stijl.
 - Aangezien de gegevens in vectorvorm worden geleverd, is er minder serververwerking nodig om de gegevens voor te bereiden. Hierdoor kunnen de nieuwere gegevens sneller beschikbaar worden gesteld.

Alle lagen die een vectorbron `sourceLayer` gebruiken, moeten een waarde opgeven.

Eenmaal gemaakt, kunnen gegevensbronnen worden toegevoegd `map.sources` aan de kaart via de eigenschap, die een [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager)is. In de volgende code `DataSource` ziet u hoe u een maakt en deze aan de kaart toevoegt.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure Maps houdt zich aan de [Mapbox Vector Tile Specification](https://github.com/mapbox/vector-tile-spec), een open standaard.

## <a name="connecting-a-data-source-to-a-layer"></a>Een gegevensbron verbinden met een laag

Gegevens worden weergegeven op de kaart met behulp van renderinglagen. Een enkele gegevensbron kan worden verwezen door een of meer renderinglagen. Voor de volgende renderinglagen is een gegevensbron vereist:

- [Bellenlaag](map-add-bubble-layer.md) - hiermee worden puntgegevens weergegeven als geschaalde cirkels op de kaart.
- [Symboollaag](map-add-pin.md) - hiermee worden puntgegevens weergegeven als pictogrammen of tekst.
- [Heat map laag](map-add-heat-map-layer.md) - geeft puntgegevens weer als een dichtheidswarmtekaart.
- [Lijnlaag](map-add-shape.md) - maak een lijn weer en of maak de omtrek van veelhoeken weer. 
- [Veelhoeklaag](map-add-shape.md) - vult het gebied van een veelhoek met een effen kleur of afbeeldingspatroon.

In de volgende code ziet u hoe u een gegevensbron maakt, deze aan de kaart toevoegt en deze aansluit op een bellenlaag. En importeer vervolgens GeoJSON-puntgegevens van een externe locatie in de gegevensbron. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Er zijn extra renderinglagen die geen verbinding maken met deze gegevensbronnen, maar ze laden de gegevens direct voor rendering. 

- [Afbeeldingslaag](map-add-image-layer.md) - overlays één afbeelding boven op de kaart en bindt de hoeken aan een set opgegeven coördinaten.
- [Tegellaag](map-add-tile-layer.md) - legt een rastertegellaag boven op de kaart.

## <a name="one-data-source-with-multiple-layers"></a>Eén gegevensbron met meerdere lagen

Meerdere lagen kunnen worden aangesloten op één gegevensbron. Er zijn veel verschillende scenario's waarin deze optie nuttig is. Denk bijvoorbeeld aan het scenario waarin een gebruiker een veelhoek tekent. We moeten het veelhoekgebied weergeven en vullen terwijl de gebruiker punten aan de kaart toevoegt. Als u een gestileerde lijn toevoegt om de veelhoek te schetsen, u de randen van de veelhoek gemakkelijker zien terwijl de gebruiker tekent. Als u een afzonderlijke positie in de veelhoek gemakkelijk wilt bewerken, kunnen we boven elke positie een handgreep, zoals een pin of een markering, toevoegen.

![Kaart met meerdere lagen die gegevens renderen van één gegevensbron](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

In de meeste toewijzingsplatforms hebt u een veelhoekobject, een lijnobject en een pin nodig voor elke positie in de veelhoek. Als de veelhoek wordt gewijzigd, moet u de lijn en pinnen handmatig bijwerken, wat snel complex kan worden.

Met Azure Maps hebt u alleen maar één veelhoek in een gegevensbron nodig, zoals in de onderstaande code wordt weergegeven.

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

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Datasource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Gegevensbronopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource (VectorTileSource)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten toevoegen:

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
> [Een warmtekaart toevoegen](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)