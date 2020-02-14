---
title: Clustering Point-gegevens op een kaart | Microsoft Azure kaarten
description: In dit artikel leert u hoe u cluster Point-gegevens kunt weer geven op een kaart met behulp van de Web-SDK van Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: e65681aefc047ba540d4ad0d91ef6e4d2af5f3ca
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190264"
---
# <a name="clustering-point-data"></a>Clustering Point-gegevens

Bij het visualiseren van veel gegevens punten op de kaart kunnen gegevens punten van elkaar overlappen. De overlap ping kan ertoe leiden dat de kaart onleesbaar wordt en moeilijk te gebruiken is. Clustering Point-gegevens zijn het proces van het samen voegen van punt gegevens die zich in de buurt van elkaar bevinden en als één geclusterd gegevens punt vertegenwoordigen. Wanneer de gebruiker inzoomt op de kaart, worden de clusters in hun afzonderlijke gegevens punten gesplitst. Wanneer u met een groot aantal gegevens punten werkt, gebruikt u de cluster processen om uw gebruikers ervaring te verbeteren.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Clustering inschakelen op een gegevens bron

Schakel clustering in de klasse `DataSource` in door de `cluster` optie in te stellen op True. Stel `ClusterRadius` in om nabijgelegen punten te selecteren en deze te combi neren in een cluster. De waarde van `ClusterRadius` is in pixels. Gebruik `clusterMaxZoom` om een zoom niveau op te geven waarmee de cluster logica wordt uitgeschakeld. Hier volgt een voor beeld van het inschakelen van clustering in een gegevens bron.

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
> Als twee gegevens punten dicht bij elkaar staan, is het mogelijk dat het cluster nooit gesplitst is, ongeacht hoe dicht de gebruiker in-of uitzoomt. Om dit op te lossen, kunt u de optie `clusterMaxZoom` instellen om de logica van het cluster uit te scha kelen en alleen alles weer te geven.

Hier worden extra methoden beschreven die de `DataSource` klasse biedt voor het clusteren:

| Methode | Retour type | Beschrijving |
|--------|-------------|-------------|
| getClusterChildren (clusterId: getal) | Promise&lt;matrix&lt;functie&lt;geometrie, een&gt; \| vorm&gt;&gt; | Hiermee worden de onderliggende items van het opgegeven cluster op het volgende zoom niveau opgehaald. Deze onderliggende elementen kunnen bestaan uit een combi natie van vormen en subclusteren. De subclusteren zijn functies met eigenschappen die overeenkomen met ClusteredProperties. |
| getClusterExpansionZoom (clusterId: getal) | &lt;nummer van Promise&gt; | Hiermee wordt een zoom niveau berekend waarmee het cluster wordt uitgebreid of gesplitst. |
| getClusterLeaves (clusterId: aantal, limiet: aantal, verschuiving: getal) | Promise&lt;matrix&lt;functie&lt;geometrie, een&gt; \| vorm&gt;&gt; | Haalt alle punten in een cluster op. Stel de `limit` in om een subset van de punten te retour neren en gebruik de `offset` om door de punten te bladeren. |

## <a name="display-clusters-using-a-bubble-layer"></a>Clusters weer geven met behulp van een Bubble Layer

Een Bubble slaag is een uitstekende manier om geclusterde punten weer te geven. Gebruik expressies om de RADIUS te schalen en de kleur te wijzigen op basis van het aantal punten in het cluster. Als u clusters met behulp van een Bubble laag weergeeft, moet u een afzonderlijke laag gebruiken om niet-geclusterde gegevens punten weer te geven.

Als u de grootte van het cluster boven op de bel wilt weer geven, gebruikt u een symbool laag met tekst en gebruikt u geen pictogram.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Basis clusters voor bellen lagen" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>Basic Bubble Layer clustering</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Clusters weer geven met behulp van een symbool-laag

Bij het visualiseren van gegevens punten worden symbolen die elkaar overlappen, automatisch verborgen om ervoor te zorgen dat een schone gebruikers interface wordt weer gegeven. Dit standaard gedrag kan ongewenst zijn als u de densiteit van gegevens punten op de kaart wilt weer geven. Deze instellingen kunnen echter worden gewijzigd. Als u alle symbolen wilt weer geven, stelt u de optie `allowOverlap` van de eigenschap Symbol Layers `iconOptions` in op `true`. 

Gebruik clustering om de densiteit van gegevens punten weer te geven en een schone gebruikers interface te houden. In het onderstaande voor beeld ziet u hoe u aangepaste symbolen kunt toevoegen en hoe u clusters en afzonderlijke gegevens punten kunt weer geven met behulp van de Symbol-laag.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Laag geclusterde symbolen" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>laag geclusterde symbool Layer</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering en de laag met hitte kaarten

Hitte kaarten zijn een uitstekende manier om de densiteit van gegevens op de kaart weer te geven. Deze visualisatie methode kan een groot aantal gegevens punten zelf verwerken. Als de gegevens punten geclusterd zijn en de cluster grootte wordt gebruikt als het gewicht van de heatmap, kan de heatmap nog meer gegevens verwerken. Als u deze optie wilt instellen, stelt u de `weight` optie van de heatmap van de kaart in op `['get', 'point_count']`. Wanneer de RADIUS van het cluster klein is, zal de heatmap er bijna hetzelfde uitzien als een heatmap met behulp van de niet-geclusterde gegevens punten, maar wordt het veel beter. Hoe kleiner de cluster RADIUS, des te nauw keuriger de heatmap is, maar met minder prestatie voordelen.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Heatmap voor cluster met gewicht" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie het overzicht van de <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>gewogen heatmap</a> van het pen-Cluster per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Muis gebeurtenissen op geclusterde gegevens punten

Wanneer er muis gebeurtenissen optreden op een laag die geclusterde gegevens punten bevat, keert het geclusterde gegevens punt terug naar de gebeurtenis als een functie object van een geojson-punt. Deze punt functie heeft de volgende eigenschappen:

| Naam van eigenschap             | Type    | Beschrijving   |
|---------------------------|---------|---------------|
| `cluster`                 | booleaans | Hiermee wordt aangegeven of de functie een cluster vertegenwoordigt. |
| `cluster_id`              | tekenreeks  | Een unieke ID voor het cluster die kan worden gebruikt met de methoden data source `getClusterExpansionZoom`, `getClusterChildren`en `getClusterLeaves`. |
| `point_count`             | getal  | Het aantal punten dat het cluster bevat.  |
| `point_count_abbreviated` | tekenreeks  | Een teken reeks die de `point_count` waarde verkort als deze lang is. (4.000 wordt bijvoorbeeld 4.000)  |

In dit voor beeld wordt een Bubble laag gebruikt die cluster punten rendert en een Click-gebeurtenis toevoegt. Wanneer de knop gebeurtenis triggers wordt geactiveerd, wordt de kaart met de code berekend en ingezoomd op het volgende zoom niveau, waarbij het cluster wordt gesplitst. Deze functionaliteit wordt geïmplementeerd met behulp van de methode `getClusterExpansionZoom` van de klasse `DataSource` en de eigenschap `cluster_id` van het geklikte geclusterde gegevens punt.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster-getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie het Azure Maps <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) van de pen-cluster op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Cluster gebied weer geven 

De punt gegevens die een cluster vertegenwoordigt, worden verdeeld over een gebied. In dit voor beeld wanneer de muis aanwijzer over een cluster wordt gehouden, worden er twee belang rijke gedragingen uitgevoerd. Eerst worden de afzonderlijke gegevens punten in het cluster gebruikt voor het berekenen van een convexe romp. Vervolgens wordt de convexe romp op de kaart weer gegeven om een gebied weer te geven.  Een convexe-romp is een veelhoek die een set punten omregelt zoals een elastische band en kan worden berekend met behulp van de `atlas.math.getConvexHull` methode. Alle punten die deel uitmaken van een cluster kunnen worden opgehaald uit de gegevens bron met behulp van de `getClusterLeaves` methode.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Cluster gebied convexe romp" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen- <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>cluster gebied convexe romp</a> per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Gegevens in clusters samen voegen

Vaak worden clusters weer gegeven met behulp van een symbool met het aantal punten dat zich binnen het cluster bevindt. Maar soms is het wenselijk om de stijl van clusters met extra metrische gegevens aan te passen. Met cluster aggregaties kunnen aangepaste eigenschappen worden gemaakt en gevuld met behulp van een [statistische expressie](data-driven-style-expressions-web-sdk.md#aggregate-expression) berekening.  U kunt cluster aggregaties definiëren in `clusterProperties` optie van de `DataSource`.

In het volgende voor beeld wordt een statistische expressie gebruikt. De code berekent een telling op basis van de eigenschap entiteit type van elk gegevens punt in een cluster. Wanneer een gebruiker op een cluster klikt, wordt er een pop-upvenster weer gegeven met aanvullende informatie over het cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggregaties van het cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>aggregaties</a> van het pen-cluster Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Gegevens bron klasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions-object](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [naam ruimte van Atlas. math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Zie code voorbeelden om functionaliteit toe te voegen aan uw app:

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Een symbool laag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"]
> [Een hitte kaart laag toevoegen](map-add-heat-map-layer.md)
