---
title: Clustergegevens van het clusterpunt op een kaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u puntgegevens clusteren en deze op een kaart renderen met de Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: e65681aefc047ba540d4ad0d91ef6e4d2af5f3ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190264"
---
# <a name="clustering-point-data"></a>Clustergegevens van clusterpunten

Bij het visualiseren van veel gegevenspunten op de kaart kunnen gegevenspunten elkaar overlappen. De overlapping kan ertoe leiden dat de kaart onleesbaar en moeilijk te gebruiken is. Clustering point data is het proces van het combineren van puntgegevens die dicht bij elkaar liggen en deze op de kaart weergeven als één geclusterd gegevenspunt. Als de gebruiker inzoomt op de kaart, de clusters uit elkaar te halen in hun individuele gegevenspunten. Wanneer u met een groot aantal gegevenspunten werkt, gebruikt u de clusteringprocessen om uw gebruikerservaring te verbeteren.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Clustering op een gegevensbron inschakelen

Schakel clustering `DataSource` in de `cluster` klasse in door de optie true in te stellen. Stel `ClusterRadius` in op het selecteren van punten in de buurt en combineer deze in een cluster. De waarde `ClusterRadius` van is in pixels. Gebruik `clusterMaxZoom` om een zoomniveau op te geven waarop de clusterlogica moet worden uitgeschakeld. Hier is een voorbeeld van hoe u clustering in een gegevensbron inschakelt.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Als twee gegevenspunten dicht bij elkaar liggen op de grond, is het mogelijk dat het cluster nooit uit elkaar valt, ongeacht hoe dicht de gebruiker inzoomt. Om dit aan te `clusterMaxZoom` pakken, u de optie instellen om de clusterlogica uit te schakelen en gewoon alles weer te geven.

Hier volgen aanvullende methoden `DataSource` die de klasse biedt voor clustering:

| Methode | Retourtype | Beschrijving |
|--------|-------------|-------------|
| getClusterChildren(clusterId: getal) | Promise&lt;&lt;Array&lt;Feature Geometry, elke&gt; \| vorm&gt;&gt; | Hiermee haalt u de kinderen van het gegeven cluster op het volgende zoomniveau op. Deze kinderen kunnen een combinatie zijn van vormen en subclusters. De subclusters zijn functies met eigenschappen die overeenkomen met ClusteredProperties. |
| getClusterExpansionZoom(clusterId: getal) | Beloftenummer&lt;&gt; | Berekent een zoomniveau waarop het cluster begint uit te breiden of uit elkaar te vallen. |
| getClusterLeaves(clusterId: getal, limiet: getal, verschuiving: getal) | Promise&lt;&lt;Array&lt;Feature Geometry, elke&gt; \| vorm&gt;&gt; | Hiermee worden alle punten in een cluster opgehaald. Stel `limit` de set in om een subset `offset` van de punten terug te geven en gebruik de pagina naar door de punten. |

## <a name="display-clusters-using-a-bubble-layer"></a>Clusters weergeven met behulp van een bellenlaag

Een bellenlaag is een geweldige manier om geclusterde punten weer te geven. Gebruik expressies om de straal te schalen en de kleur te wijzigen op basis van het aantal punten in het cluster. Als u clusters weergeeft met behulp van een bellenlaag, moet u een afzonderlijke laag gebruiken om niet-geclusterde gegevenspunten weer te geven.

Als u de grootte van het cluster boven op de bel wilt weergeven, gebruikt u een symboollaag met tekst en gebruikt u geen pictogram.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clustering van basisbellenlagen" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>penbasisringlaagclustering</a> door<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Clusters weergeven met een symboollaag

Bij het visualiseren van gegevenspunten verbergt de symboollaag automatisch symbolen die elkaar overlappen om een schonere gebruikersinterface te garanderen. Dit standaardgedrag kan ongewenst zijn als u de dichtheid van gegevenspunten op de kaart wilt weergeven. Deze instellingen kunnen echter worden gewijzigd. Als u alle symbolen `allowOverlap` wilt weergeven, stelt u de optie van de eigenschap Symboollagen `iconOptions` in op `true`. 

Gebruik clustering om de dichtheid van gegevenspunten weer te geven terwijl een schone gebruikersinterface behouden blijft. In het onderstaande voorbeeld ziet u hoe u aangepaste symbolen toevoegt en clusters en afzonderlijke gegevenspunten weergeeft met behulp van de symboollaag.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clusterdesymboollaag" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>laag Pen clustered</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>symbol van Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering en de laag heatmaps

Heat maps zijn een geweldige manier om de dichtheid van gegevens weer te geven op de kaart. Deze visualisatiemethode kan een groot aantal gegevenspunten alleen verwerken. Als de gegevenspunten zijn geclusterd en de clustergrootte wordt gebruikt als het gewicht van de warmtekaart, kan de warmtekaart nog meer gegevens verwerken. Als u deze optie `weight` wilt bereiken, stelt `['get', 'point_count']`u de optie van de heatmaplaag in op . Wanneer de clusterradius klein is, ziet de warmtekaart er bijna identiek uit als een heatmap met behulp van de niet-geclusterde gegevenspunten, maar het zal veel beter presteren. Echter, hoe kleiner de cluster radius, hoe nauwkeuriger de warmtekaart zal zijn, maar met minder prestatievoordelen.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster gewogen warmtekaart" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>met pencluster gewogen heatmap van</a> Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Muisgebeurtenissen op geclusterde gegevenspunten

Wanneer muisgebeurtenissen optreden op een laag die geclusterde gegevenspunten bevat, keert het geclusterde gegevenspunt terug naar de gebeurtenis als een functieobject voor geoJSON-punten. Deze puntfunctie heeft de volgende eigenschappen:

| Naam van eigenschap             | Type    | Beschrijving   |
|---------------------------|---------|---------------|
| `cluster`                 | booleaans | Geeft aan of de functie een cluster vertegenwoordigt. |
| `cluster_id`              | tekenreeks  | Een unieke id voor het cluster dat `getClusterExpansionZoom`kan `getClusterChildren`worden `getClusterLeaves` gebruikt met de DataSource en methoden. |
| `point_count`             | getal  | Het aantal punten dat het cluster bevat.  |
| `point_count_abbreviated` | tekenreeks  | Een tekenreeks die de `point_count` waarde verkort als deze lang is. (4.000 wordt bijvoorbeeld 4K)  |

In dit voorbeeld wordt een bellaag gebruikt die clusterpunten rendert en een klikgebeurtenis toevoegt. Wanneer de klikgebeurtenis wordt geactiveerd, berekent en zoomt de code in op het volgende zoomniveau, waarbij het cluster uit elkaar valt. Deze functionaliteit wordt geïmplementeerd `getClusterExpansionZoom` met behulp `DataSource` van `cluster_id` de methode van de klasse en de eigenschap van het geklikte geclusterde gegevenspunt.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie het <a href='https://codepen.io/azuremaps/pen/moZWeV/'>pencluster getClusterExpansionZoom</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Clustergebied weergeven 

De puntgegevens die een cluster vertegenwoordigt, zijn verspreid over een gebied. In dit voorbeeld wanneer de muis boven een cluster wordt gezweefd, treden twee hoofdgedragingen op. Ten eerste worden de afzonderlijke gegevenspunten in het cluster gebruikt om een bolle romp te berekenen. Vervolgens wordt de bolle romp op de kaart weergegeven om een gebied weer te geven.  Een bolle romp is een veelhoek die een set punten omwikkelt als een elastische band en kan worden berekend met behulp van de `atlas.math.getConvexHull` methode. Alle punten in een cluster kunnen met behulp van `getClusterLeaves` de methode uit de gegevensbron worden opgehaald.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Clustergebied bolle romp" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>convexe romp van</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>het penclustergebied door Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Gegevens aggregeren in clusters

Vaak worden clusters weergegeven met behulp van een symbool met het aantal punten dat zich binnen het cluster begeeft. Maar soms is het wenselijk om de stijl van clusters aan te passen met extra statistieken. Met clusteraggregaten kunnen aangepaste eigenschappen worden gemaakt en ingevuld met behulp van een [geaggregeerde expressieberekening.](data-driven-style-expressions-web-sdk.md#aggregate-expression)  Clusteraggregaten kunnen worden `clusterProperties` gedefinieerd in `DataSource`de optie van de .

In het volgende voorbeeld wordt een geaggregeerde expressie gebruikt. De code berekent een telling op basis van de eigenschap entiteitstype van elk gegevenspunt in een cluster. Wanneer een gebruiker op een cluster klikt, wordt een pop-up weergegeven met aanvullende informatie over het cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Clusteraggregaten" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>penclusteraggregaten</a> van<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [DataSource, klasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Object DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math naamruimte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Zie codevoorbeelden om functionaliteit toe te voegen aan uw app:

> [!div class="nextstepaction"]
> [Een bubbellaag toevoegen](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"]
> [Een heatmap-laag toevoegen](map-add-heat-map-layer.md)
