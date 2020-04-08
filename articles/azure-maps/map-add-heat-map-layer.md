---
title: Een heatmaplaag toevoegen aan een kaart | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over het toevoegen van een warmtekaartlaag aan een kaart met behulp van de Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 19765bd28f365cc6f6d5b06646896613dd3e3e87
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804551"
---
# <a name="add-a-heat-map-layer"></a>Een heatmap-laag toevoegen

Heat maps, ook wel puntdichtheidskaarten genoemd, zijn een type gegevensvisualisatie. Ze worden gebruikt om de dichtheid van gegevens weer te geven met behulp van een reeks kleuren en tonen de gegevens "hot spots" op een kaart. Heat maps zijn een geweldige manier om datasets met een groot aantal punten weer te geven. 

Het renderen van tienduizenden punten als symbolen kan betrekking hebben op het grootste deel van de kaart gebied. Deze zaak resulteert waarschijnlijk in veel symbolen overlappen. Waardoor het moeilijk is om een beter begrip van de gegevens te krijgen. Het visualiseren van dezelfde gegevensset als een heatmap maakt het echter gemakkelijk om de dichtheid en de relatieve dichtheid van elk gegevenspunt te zien.

U heatmaps gebruiken in veel verschillende scenario's, waaronder:

- **Temperatuurgegevens**: Geeft benaderingen voor wat de temperatuur is tussen twee gegevenspunten.
- **Gegevens voor geluidssensoren**: Toont niet alleen de intensiteit van het geluid waar de sensor zich bevindt, maar het kan ook inzicht geven in de dissipatie over een afstand. Het geluidsniveau op een enkele locatie is mogelijk niet hoog. Als het geluidsdekkingsgebied van meerdere sensoren elkaar overlapt, is het mogelijk dat dit overlappende gebied hogere geluidsniveaus ervaart. Als zodanig zou het overlappende gebied zichtbaar zijn in de heatmap.
- **GPS-trace:** Omvat de snelheid als een gewogen hoogte kaart, waar de intensiteit van elk gegevenspunt is gebaseerd op de snelheid. Deze functionaliteit biedt bijvoorbeeld een manier om te zien waar een voertuig te hard reed.

> [!TIP]
> Warmtekaartlagen geven standaard de coördinaten van alle geometrieën weer in een gegevensbron. Als u de laag wilt beperken zodat alleen `filter` de kenmerken van `['==', ['geometry-type'], 'Point']`puntgeometrie worden weergegeven, stelt u de eigenschap van de laag in op . Als u ook MultiPoint-functies wilt `filter` opnemen, stelt `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`u de eigenschap van de laag in op .

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Een heatmap-laag toevoegen

Als u een gegevensbron van punten als een warmtekaart wilt `HeatMapLayer` weergeven, geeft u uw gegevensbron door in een instantie van de klasse en voegt u deze toe aan de kaart.

In de volgende code heeft elk warmtepunt een straal van 10 pixels op alle zoomniveaus. Om een betere gebruikerservaring te garanderen, bevindt de heatmap zich onder de labellaag. De labels blijven duidelijk zichtbaar. De gegevens in deze steekproef is van de [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/). Het is voor aanzienlijke aardbevingen die zich hebben voorgedaan in de afgelopen 30 dagen.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Hier is het volledige voorbeeld van de code van de voorgaande code.

<br/>

<iframe height='500' scrolling='no' title='Eenvoudige heatmaplaag' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen Simple Heat Map<a href='https://codepen.io/azuremaps'>@azuremaps</a>Layer <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>by</a> Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>De heatmaplaag aanpassen

In het vorige voorbeeld werd de heatmap aangepast door de straal- en dekkingsopties in te stellen. De heatmaplaag biedt verschillende opties voor aanpassing, waaronder:

* `radius`: Hiermee definieert u een pixelstraal waarin elk gegevenspunt kan worden weergegeven. U de straal instellen als een vast getal of als expressie. Door een expressie te gebruiken, u de straal schalen op basis van het zoomniveau en een consistent ruimtelijk gebied op de kaart weergeven (bijvoorbeeld een straal van 5 mijl).
* `color`: Hiermee geeft u op hoe de heatmap wordt gekleurd. Een kleurverloop is een veelvoorkomend kenmerk van warmtekaarten. U het effect `interpolate` bereiken met een expressie. U ook `step` een expressie gebruiken voor het inkleuren van de warmtekaart, waardoor de dichtheid visueel wordt opgesplitst in bereiken die lijken op een contour- of radarstijlkaart. Deze kleurenpaletten definiëren de kleuren van het minimum tot de maximale dichtheidswaarde. 

  U geeft kleurwaarden voor warmtekaarten `heatmap-density` op als expressie op de waarde. De kleur van het gebied waar geen gegevens zijn, wordt gedefinieerd bij index 0 van de expressie 'Interpolatie' of de standaardkleur van een expressie 'Getrapt'. U deze waarde gebruiken om een achtergrondkleur te definiëren. Vaak is deze waarde ingesteld op transparant, of een semi-transparante zwart. 
   
  Hier volgen voorbeelden van kleurexpressies:

  | Interpolatiekleurexpressie | Getrapte kleurexpressie | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpolaat",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["lineair"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["heatmap-dichtheid"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transparant",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, 'paars',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'stap',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["heatmap-dichtheid"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"transparant",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'marine',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, 'groen',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, 'geel',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, 'rood'<br/>\] |   

- `opacity`: Hiermee geeft u op hoe ondoorzichtig of transparant de warmtekaartlaag is.
- `intensity`: Past een vermenigvuldiger toe op het gewicht van elk gegevenspunt om de algehele intensiteit van de heatmap te verhogen. Het veroorzaakt een verschil in het gewicht van gegevenspunten, waardoor het gemakkelijker te visualiseren.
- `weight`: Standaard hebben alle gegevenspunten een gewicht van 1 en zijn ze gelijk gewogen. De gewichtsoptie fungeert als een vermenigvuldiger en u deze instellen als een getal of een expressie. Als een getal is ingesteld als het gewicht, is het de gelijkwaardigheid van het twee keer op de kaart plaatsen van elk gegevenspunt. Bijvoorbeeld, als het gewicht 2 is, dan verdubbelt de dichtheid. Als u de gewichtsoptie instelt op een getal, wordt de warmtekaart op een vergelijkbare manier weergegeven als met de intensiteitsoptie. 

  Als u echter een expressie gebruikt, kan het gewicht van elk gegevenspunt worden gebaseerd op de eigenschappen van elk gegevenspunt. Stel dat elk gegevenspunt een aardbeving vertegenwoordigt. De magnitude waarde is een belangrijke statistiek voor elke aardbeving gegevenspunt. Aardbevingen gebeuren de hele tijd, maar de meeste hebben een lage omvang, en worden niet opgemerkt. Gebruik de waarde van de grootte in een expressie om het gewicht aan elk gegevenspunt toe te wijzen. Door de groottewaarde te gebruiken om het gewicht toe te wijzen, krijgt u een betere weergave van de betekenis van aardbevingen binnen de hittekaart.
- `source`en: `source-layer`U de gegevensbron bijwerken.

Hier is een hulpmiddel om de verschillende opties voor warmtekaartenlaag uit te testen.

<br/>

<iframe height='700' scrolling='no' title='Opties voor warmtekaartlaag' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>penheatmaplaagopties</a> van<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Consistente zoombare warmtekaart

Standaard hebben de stralen van gegevenspunten die in de heatmaplaag worden weergegeven, een vaste pixelstraal voor alle zoomniveaus. Terwijl u de kaart inzoomt, worden de gegevens samengevoegd en ziet de warmtekaartlaag er anders uit. 

Gebruik `zoom` een expressie om de straal voor elk zoomniveau te schalen, zodat elk gegevenspunt hetzelfde fysieke gebied van de kaart bestrijkt. Deze expressie zorgt ervoor dat de heatmaplaag er statischer en consistenter uitziet. Elk zoomniveau van de kaart heeft twee keer zoveel pixels verticaal en horizontaal als het vorige zoomniveau. 

Als u de straal zo schaalt dat deze wordt verdubbelt met elk zoomniveau, wordt een warmtekaart gemaakt die er consistent uitziet op alle zoomniveaus. Als u deze `zoom` schaling wilt `exponential interpolation` toepassen, gebruikt u met een basisexpressie 2, waarbij de pixelstraal is ingesteld voor het minimale zoomniveau en een geschaalde straal voor het maximale zoomniveau dat wordt berekend zoals `2 * Math.pow(2, minZoom - maxZoom)` weergegeven in het volgende voorbeeld. Zoom op de kaart in om te zien hoe de warmtekaart wordt geschaald met het zoomniveau.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Consistente zoombare warmtekaart" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>penconsistente zoombare warmtekaart</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Wanneer u clustering op de gegevensbron inschakelt, worden punten die dicht bij elkaar liggen gegroepeerd als een geclusterd punt. U het puntenaantal van elk cluster gebruiken als de gewichtsexpressie voor de warmtekaart. Dit kan het aantal punten dat moet worden weergegeven aanzienlijk verminderen. Het aantal punten van een `point_count` cluster wordt opgeslagen in een eigenschap van de puntfunctie: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Als de clusterstraal slechts een paar pixels is, zou er een klein visueel verschil in de weergave zijn. Een grotere straal groepeert meer punten in elk cluster en verbetert de prestaties van de heatmap.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [HeatMapLayer (HeatMapLayer)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten wilt toevoegen:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)
