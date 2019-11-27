---
title: Clustering Point-gegevens in Azure Maps | Microsoft Docs
description: Cluster Point-gegevens in de Web-SDK
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 4a583f77aac036028fd75d3c05af805031f08ebd
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480565"
---
# <a name="clustering-point-data"></a>Clustering Point-gegevens

Wanneer een groot aantal gegevens punten op de kaart wordt gevisualiseerd, overlappen punten elkaar, de kaart lijkt overzichtelijker en wordt deze moeilijk te zien en te gebruiken. Het clusteren van punt gegevens kan worden gebruikt om deze gebruikers ervaring te verbeteren. Clustering Point-gegevens zijn het proces van het samen voegen van punt gegevens die zich in de buurt van elkaar bevinden en als één geclusterd gegevens punt vertegenwoordigen. Wanneer de gebruiker inzoomt op de kaart, worden de clusters in hun afzonderlijke gegevens punten gesplitst.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Clustering inschakelen op een gegevens bron

Clustering kan eenvoudig worden ingeschakeld op de klasse `DataSource` door de `cluster`-optie in te stellen op True. Daarnaast kan de pixel RADIUS om nabijgelegen punten te selecteren die in een cluster moeten worden gecombineerd, worden ingesteld met behulp van de `clusterRadius` en kan er een zoom niveau worden opgegeven om de cluster logica uit te scha kelen met behulp van de `clusterMaxZoom` optie. Hier volgt een voor beeld van het inschakelen van clustering in een gegevens bron.

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
> Als twee gegevens punten dicht bij elkaar staan, is het mogelijk dat het cluster nooit gesplitst is, ongeacht hoe dicht de gebruiker in-/uitzoomen. Om dit op te lossen, kunt u de `clusterMaxZoom` optie van de gegevens bron instellen, die op het zoom niveau opgeeft om de cluster logica uit te scha kelen en alleen alles weer te geven.

De klasse `DataSource` heeft ook de volgende methoden die betrekking hebben op clustering:

| Methode | Retour type | Beschrijving |
|--------|-------------|-------------|
| getClusterChildren (clusterId: getal) | Promise&lt;matrix&lt;functie&lt;geometrie, een&gt; \| vorm&gt;&gt; | Hiermee worden de onderliggende items van het opgegeven cluster op het volgende zoom niveau opgehaald. Deze onderliggende elementen kunnen bestaan uit een combi natie van vormen en subclusteren. De subclusteren zijn functies met eigenschappen die overeenkomen met ClusteredProperties. |
| getClusterExpansionZoom (clusterId: getal) | &lt;nummer van Promise&gt; | Hiermee wordt een zoom niveau berekend waarmee het cluster wordt uitgebreid of gesplitst. |
| getClusterLeaves (clusterId: aantal, limiet: aantal, verschuiving: getal) | Promise&lt;matrix&lt;functie&lt;geometrie, een&gt; \| vorm&gt;&gt; | Haalt alle punten in een cluster op. Stel de `limit` in om een subset van de punten te retour neren en gebruik de `offset` om door de punten te bladeren. |

## <a name="display-clusters-using-a-bubble-layer"></a>Clusters weer geven met behulp van een Bubble Layer

Een Bubble-laag is een uitstekende manier om geclusterde punten te renderen, omdat u de RADIUS eenvoudig kunt schalen en de kleur wijzigt op basis van het aantal punten in het cluster met behulp van een expressie. Bij het weer geven van clusters met behulp van een tekenlaag moet u ook een afzonderlijke laag gebruiken voor het weer geven van niet-geclusterde gegevens punten. Het is vaak handig om ook de grootte van het cluster boven op de bellen weer te geven. Een symbool laag met tekst en er kan geen pictogram worden gebruikt om dit gedrag te vertonen. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Basis clusters voor bellen lagen" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>Basic Bubble Layer clustering</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Clusters weer geven met behulp van een symbool-laag

Wanneer de punt gegevens worden gevisualiseerd met behulp van de symbool-laag, worden de symbolen die elkaar overlappen, standaard automatisch verborgen om een overzichtelijke ervaring te creëren. Dit is echter niet de gewenste ervaring als u de densiteit van gegevens punten op de kaart wilt zien. Als u de optie `allowOverlap` van de eigenschap Symbol Layers `iconOptions` instelt op `true` wordt deze ervaring uitgeschakeld, maar worden alle symbolen weer gegeven. Met clustering kunt u de dichtheid van alle gegevens bekijken tijdens het maken van een goed gereinigde gebruikers ervaring. In dit voor beeld worden aangepaste symbolen gebruikt om clusters en afzonderlijke gegevens punten weer te geven.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Laag geclusterde symbolen" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>laag geclusterde symbool Layer</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering en de laag met hitte kaarten

Hitte kaarten zijn een uitstekende manier om de densiteit van gegevens op de kaart weer te geven. Deze visualisatie kan een groot aantal gegevens punten op zichzelf verwerken, maar kan nog meer gegevens verwerken als de gegevens punten geclusterd zijn en de cluster grootte wordt gebruikt als het gewicht van de heatmap. Stel de `weight` optie van de heatmap van de hitte in op `['get', 'point_count']` om dit te verzorgen. Wanneer de RADIUS van het cluster klein is, ziet de heatmap er bijna hetzelfde uit als een heatmap met behulp van de niet-geclusterde gegevens punten. Hoe kleiner de cluster RADIUS, des te nauw keuriger de heatmap, maar met minder prestatie voordelen.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Heatmap voor cluster met gewicht" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie het overzicht van de <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>gewogen heatmap</a> van het pen-Cluster per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Muis gebeurtenissen op geclusterde gegevens punten

Wanneer er muis gebeurtenissen optreden op een laag die geclusterde gegevens punten bevat, wordt het geclusterde gegevens punt geretourneerd naar de gebeurtenis als een functie object van een geojson-punt. Deze punt functie heeft de volgende eigenschappen:

| Naam van eigenschap | Type | Beschrijving |
|---------------|------|-------------|
| cluster | booleaans | Hiermee wordt aangegeven of de functie een cluster vertegenwoordigt. |
| cluster_id | tekenreeks | Een unieke ID voor het cluster die kan worden gebruikt met de methoden data source `getClusterExpansionZoom`, `getClusterChildren`en `getClusterLeaves`. |
| point_count | getal | Het aantal punten dat het cluster bevat. |
| point_count_abbreviated | tekenreeks | Een teken reeks die de `point_count` waarde verkort als deze lang is. (4.000 wordt bijvoorbeeld 4.000) |

In dit voor beeld wordt een Bubble laag gebruikt waarmee cluster punten worden gerenderd en een Click-gebeurtenis wordt toegevoegd waarmee de toewijzing wordt geactiveerd, berekend en ingezoomd op het volgende zoom niveau waarbij het cluster wordt gesplitst met behulp van de `getClusterExpansionZoom` methode van de `DataSource` klasse en de `cluster_id` eigenschap van het geklikte geclusterde gegevens punt. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster-getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie het Azure Maps <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) van de pen-cluster op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Cluster gebied weer geven 

De punt gegevens die een cluster vertegenwoordigt, worden verdeeld over een gebied. In dit voor beeld wanneer de muis aanwijzer zich boven een cluster bevindt, worden de afzonderlijke gegevens punten die het bevat (bladeren) gebruikt voor het berekenen van een convexe romp en weer gegeven op de kaart om het gebied weer te geven. Alle punten die deel uitmaken van een cluster kunnen worden opgehaald uit de gegevens bron met behulp van de `getClusterLeaves` methode. Een convexe-romp is een veelhoek die een set punten omregelt zoals een elastische band en kan worden berekend met behulp van de `atlas.math.getConvexHull` methode.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Cluster gebied convexe romp" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen- <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>cluster gebied convexe romp</a> per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Gegevens in clusters samen voegen

Vaak worden clusters weer gegeven met behulp van een symbool met het aantal punten in het cluster, maar soms is het wenselijk om de stijl van clusters op basis van een bepaalde metriek verder aan te passen, zoals de totale omzet van alle punten in een cluster. Met cluster aggregaties kunnen aangepaste eigenschappen worden gemaakt en gevuld met behulp van een [statistische expressie](data-driven-style-expressions-web-sdk.md#aggregate-expression) berekening.  U kunt cluster aggregaties definiëren in `clusterProperties` optie van de `DataSource`.

In het volgende voor beeld wordt een statistische expressie gebruikt om een aantal te berekenen op basis van de eigenschap entiteit type van elk gegevens punt in een cluster.

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
