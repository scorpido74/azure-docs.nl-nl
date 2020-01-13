---
title: Een heatmap aan een kaart toevoegen | Microsoft Azure kaarten
description: In dit artikel vindt u informatie over het toevoegen van een heatmap aan een kaart met behulp van de Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ead30a80a6568e72f922f355916d31121b49a93b
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911210"
---
# <a name="add-a-heat-map-layer"></a>Een heatmap-laag toevoegen

Heatmap, ook wel bekend als punt-dichtheids kaarten, is een type gegevens visualisatie dat wordt gebruikt om de densiteit van gegevens te vertegenwoordigen door gebruik te maken van een reeks kleuren. Ze worden vaak gebruikt om de gegevens ' hot spots ' op een kaart weer te geven en zijn een uitstekende manier om grote punt sets te renderen. 

U kunt bijvoorbeeld tien duizenden punten in de kaart weergave weer geven als symbolen die het grootste deel van het kaart gebied bestrijken. Dit leidt waarschijnlijk tot veel symbolen die elkaar overlappen, waardoor het moeilijk is om veel inzicht te krijgen in de gegevens. Als u echter dezelfde gegevensset visualiseren als een heatmap, is het eenvoudig om te zien waar de punt gegevens de dichtste en de relatieve dichtheid van andere gebieden zijn.

U kunt heatmap gebruiken in veel verschillende scenario's, zoals:

- **Temperatuur gegevens**: biedt benaderingen voor wat de Tempe ratuur tussen twee gegevens punten is.
- **Gegevens voor ruis Sens oren**: toont niet alleen de intensiteit van de ruis waarbij de sensor zich bevindt, maar kan ook inzicht krijgen in de dissipatie over een afstand. Het geluids niveau van een site kan niet hoog zijn. Maar als het gebied van de ruis dekking van meerdere Sens oren overlapt, is het mogelijk dat dit overlappende gebied meer geluids niveaus kan ondervinden en dus zichtbaar is in de heatmap.
- **GPS-tracering**: bevat de snelheid als een kaart met een gewogen hoogte, waarbij de intensiteit van elk gegevens punt is gebaseerd op de snelheid. Dit biedt bijvoorbeeld een manier om te zien waar een voer tuig versnellen.

> [!TIP]
> Hitte kaart lagen standaard worden de coördinaten van alle geometrieën in een gegevens bron weer gegeven. Als u de laag wilt beperken zodat deze alleen functies van punt geometrie weergeeft, stelt u de eigenschap `filter` van de laag in op `['==', ['geometry-type'], 'Point']`. Als u ook multi point-functies wilt toevoegen, stelt u de eigenschap `filter` van de laag in op `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Een heatmap-laag toevoegen

Als u een gegevens bron van punten wilt weer geven als een heatmap, geeft u uw gegevens bron door aan een instantie van de klasse `HeatMapLayer` en voegt u deze toe aan de kaart.

In de volgende code heeft elk verwarmings punt een straal van 10 pixels op alle zoom niveaus. Wanneer u de heatmap aan de kaart toevoegt, wordt deze onder de label laag ingevoegd om een betere gebruikers ervaring te creëren. De labels zijn duidelijk zichtbaar boven de heatmap. De gegevens in dit voor beeld zijn afkomstig van het [USGS aardings risico programma](https://earthquake.usgs.gov/)en duiden op aanzienlijke aard bevingen die in de afgelopen 30 dagen zijn opgetreden.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Hier volgt het volledige code voorbeeld van de voor gaande code.

<br/>

<iframe height='500' scrolling='no' title='Eenvoudige kaart laag voor hitte' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen- <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>eenvoudige heatmap laag</a> per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>De laag van de heatmap aanpassen

In het vorige voor beeld is de heatmap aangepast door de opties voor RADIUS en dekking in te stellen. De laag van de heatmap biedt verschillende opties voor aanpassing, waaronder:

* `radius`: definieert een pixel RADIUS waarin elk gegevens punt moet worden weer gegeven. U kunt de RADIUS instellen als een vast getal of als een expressie. U kunt met behulp van een expressie de RADIUS schalen op basis van het zoom niveau en een consistent ruimte gebied op de kaart weer geven (bijvoorbeeld een RADIUS van 5 mijl).
* `color`: Hiermee geeft u op hoe de heatmap wordt gekleurd. Een kleur overgang is een gemeen schappelijke functie van heatmap, en u kunt het effect bereiken met een `interpolate`-expressie. U kunt ook een `step`-expressie gebruiken voor het inkleuren van de heatmap, waardoor de dichtheid visueel in bereiken van een contour-of radar stijl kaart kan worden opgesplitst. Met deze kleuren paletten worden de kleuren van het minimum naar de maximale dichtheids waarde gedefinieerd. 

  U geeft de kleur waarden voor heatmap op als een expressie op de `heatmap-density` waarde. De kleur bij index 0 in een interpolatie-expressie, of de standaard kleur van een stap expressie, definieert de kleur van het gebied waar er geen gegevens zijn. U kunt dit gebruiken om een achtergrond kleur te definiëren. Deze waarde is vaak ingesteld op transparant of een semi-transparante zwart. 
   
  Hier volgen enkele voor beelden van kleur expressies:

  | Expressie voor interpolatie kleur | Expressie met stap kleur | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;interpoleing &nbsp;,<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' lineaire '\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' heatmap-dichtheid '\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, ' transparant ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, ' paars ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, ' #fb00fb ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;stap,<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' heatmap-dichtheid '\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;transparent,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, ' Navy ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, ' groen ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, ' Yellow ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, ' Red '<br/>\] |   

- `opacity`: Hiermee geeft u op hoe ondoorzichtige of transparante de laag van de heatmap.
- `intensity`: past een vermenigvuldiger toe op het gewicht van elk gegevens punt om de algehele intensiteit van de heatmap te verg Roten. Dit helpt om de kleine verschillen in het gewicht van gegevens punten gemakkelijker te maken.
- `weight`: standaard hebben alle gegevens punten een gewicht van 1 en worden ze gelijk gewogen. De optie gewicht fungeert als een vermenigvuldiger en u kunt deze instellen als een getal of een expressie. Als een getal (bijvoorbeeld 2) als het gewicht is ingesteld, is het gelijk aan het twee keer plaatsen van elk gegevens punt op de kaart, waardoor de densiteit wordt verdubbeld. Als u de optie gewicht instelt op een getal, wordt de heatmap op een vergelijk bare manier weer gegeven als met de optie intensiteit. 

  Als u echter een expressie gebruikt, kan het gewicht van elk gegevens punt worden gebaseerd op de eigenschappen van elk gegevens punt. Stel bijvoorbeeld dat elk gegevens punt een aard beving vertegenwoordigt. Een belang rijke metriek van elk bevings gegevens punt is een waarde. Aard bevingen gebeuren altijd, maar de meeste hebben een lage grootte en zijn niet zelfs vilt. Door gebruik te maken van de waarde in een expressie om het gewicht aan elk gegevens punt toe te wijzen, krijgt u een beter overzicht van de significantie van aard bevingen in de heatmap.
- `source` en `source-layer`: Hiermee kunt u de gegevens bron bijwerken.

Hier vindt u een hulp programma voor het testen van de verschillende opties voor de heatmap.

<br/>

<iframe height='700' scrolling='no' title='Opties voor de laag van de heatmap' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) van de pen-laag voor het <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>heatmap</a> op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Consistente, zoom bare heatmap

De RADIUS van gegevens punten die worden weer gegeven in de laag van de heatmap hebben standaard een straal van vaste pixels voor alle zoom niveaus. Wanneer u de kaart inzoomt, worden de gegevens samen geaggregeerd en de heatmap van de laag. 

Gebruik een `zoom`-expressie om de RADIUS voor elk zoom niveau te schalen, zodat elk gegevens punt overeenkomt met hetzelfde fysieke gebied van de kaart. Dit maakt de heatmap van de hitte laag statisch en consistent. Elk zoom niveau van de kaart heeft twee keer zoveel pixels verticaal en horizon taal als het vorige zoom niveau. 

Het schalen van de RADIUS zodat deze wordt verdubbeld met elk zoom niveau, maakt een heatmap die consistent is op alle zoom niveaus. Als u dit wilt doen, gebruikt u de `zoom` met een base 2 `exponential interpolation`-expressie, zoals in het volgende voor beeld wordt weer gegeven. Zoom in op de kaart om te zien hoe de heatmap met het zoom niveau wordt geschaald.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Consistente, zoom bare heatmap" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>consistente, zoom bare heatmap</a> met Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Wanneer u Clustering op de gegevens bron inschakelt, worden de punten die zich dicht bij elkaar bevinden, gegroepeerd als een geclusterd punt. U kunt het aantal punten van elk cluster als de gewichts expressie voor de heatmap gebruiken, en het aantal Points dat moet worden gerenderd aanzienlijk verminderen. De punt telling van een cluster wordt opgeslagen in een eigenschap `point_count` van de functie punt: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Als de cluster RADIUS slechts een paar pixels is, is er weinig visueel verschil in de rendering. Een grotere RADIUS-groep wijst meer punten in elk cluster en verbetert de prestaties van de heatmap.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw Maps:

> [!div class="nextstepaction"]
> [Een gegevens bron maken](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Op gegevens gebaseerde stijl expressies gebruiken](data-driven-style-expressions-web-sdk.md)
