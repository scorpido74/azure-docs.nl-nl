---
title: Balanceren van subgeclusterde metrische gegevens
description: Het effect van plaatsingsbeperkingen op het balanceren en hoe hiermee om te gaan
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 23782a86d31251cb1a3474e0395df716a2e832df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430641"
---
# <a name="balancing-of-subclustered-metrics"></a>Balanceren van subgeclusterde metrische gegevens

## <a name="what-is-subclustering"></a>Wat is subclustering

Subclustering vindt plaats wanneer services met verschillende plaatsingsbeperkingen een gemeenschappelijke statistiek hebben en beide belasting voor deze gegevens rapporteren. Als de belasting die door de services wordt gerapporteerd aanzienlijk verschilt, heeft de totale belasting op de knooppunten een grote standaarddeviatie en lijkt het erop dat het cluster onevenwichtig is, zelfs als het de best mogelijke balans heeft.

## <a name="how-subclustering-affects-load-balancing"></a>Hoe subclustering de taakverdeling beïnvloedt

Als de belasting die door de services op verschillende knooppunten wordt gerapporteerd aanzienlijk verschilt, kan het lijken alsof er een grote onbalans is waar er geen is. Ook als de valse onbalans veroorzaakt door subclustering groter is dan de werkelijke onbalans, heeft het de potentie om het resourcemanager-balanceringsalgoritme te verwarren en suboptimale balans in het cluster te produceren.

Stel dat we vier services hebben en dat ze allemaal een belasting voor metrische Metric1 rapporteren:

* Service A – heeft een plaatsingsbeperking 'NodeType==Type1', meldt een belasting van 10
* Service B – heeft een plaatsingsbeperking 'NodeType==Type1', meldt een belasting van 10
* Service C – heeft een plaatsingsbeperking 'NodeType==Type2', meldt een belasting van 100
* Service D – heeft een plaatsingsbeperking 'NodeType==Type2', meldt een belasting van 100
* En we hebben vier knooppunten. Twee van hen hebben NodeType ingesteld als "Type1" en de andere twee zijn "Type2"

En we hebben de volgende plaatsing:

<center>

![Voorbeeld van subclustering plaatsing][Image1]
</center>

Het cluster ziet er misschien onevenwichtig uit, we hebben een grote belasting op knooppunten 3 en 4, maar deze plaatsing creëert de best mogelijke balans in deze situatie.

Resource Manager kan subclustering situaties herkennen en in bijna alle gevallen kan het de optimale balans voor de gegeven situatie te produceren.

Voor sommige uitzonderlijke situaties waarin Resource Manager niet in staat is om een subclustered metric optimaal in evenwicht te brengen, detecteert het nog steeds subclustering en genereert het een gezondheidsrapport om u te adviseren het probleem op te lossen.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Typen subclustering en hoe hiermee wordt omgegaan

Subclusteringsituaties kunnen in drie verschillende categorieën worden ingedeeld. De categorie van een specifieke subclusteringsituatie bepaalt hoe deze wordt behandeld door Resource Manager.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Eerste categorie – platte subclustering met onsamenhangende knooppuntgroepen

Deze categorie heeft de eenvoudigste vorm van subclustering waarbij knooppunten kunnen worden gescheiden in verschillende groepen en elke service alleen op knooppunten in een van die groepen kan worden geplaatst. Elk knooppunt behoort tot één groep en slechts één groep. De hierboven beschreven situatie behoort tot deze categorie, evenals de meeste subclusteringsituaties. 

Voor de situaties in deze categorie kan de Resource Manager de optimale balans produceren en is er geen verdere interventie nodig.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Tweede categorie – subclustering met hiërarchische knooppuntgroepen

Deze situatie treedt op wanneer een groep knooppunten die zijn toegestaan voor een service een subset is van de groep knooppunten die zijn toegestaan voor een andere service. Het meest voorkomende voorbeeld van deze situatie is wanneer een bepaalde service een plaatsingsbeperking heeft gedefinieerd en een andere service geen plaatsingsbeperking heeft en op elk knooppunt kan worden geplaatst.

Voorbeeld:

* Service A: geen plaatsingsbeperking
* Service B: plaatsingsbeperking 'NodeType==Type1'
* Service C: plaatsingsbeperking 'NodeType==Type2'

Met deze configuratie wordt een subset-supersetrelatie gemaakt tussen knooppuntgroepen voor verschillende services.

<center>

![Subclusters subset superset][Image2]
</center>

In deze situatie bestaat de kans dat er een suboptimaal evenwicht wordt gemaakt.

Resource Manager herkent deze situatie en produceert een gezondheidsrapport waarin u wordt geadviseerd service A op te splitsen in twee services : Service A1 die kan worden geplaatst op Type1-knooppunten en Service A2 die op Type2-knooppunten kunnen worden geplaatst. Dit brengt ons terug naar de eerste categorie situatie die optimaal kan worden afgewogen.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Derde categorie – subclustering met gedeeltelijke overlap tussen knooppuntsets

Deze situatie treedt op wanneer er een gedeeltelijke overlap is tussen sets knooppunten waarop sommige services kunnen worden geplaatst.

Als we bijvoorbeeld een knooppunteigenschap hebben met de naam NodeColor en we hebben drie knooppunten:

* Knooppunt 1: NodeColor=Rood
* Knooppunt 2: NodeColor=Blauw
* Knooppunt 2: NodeColor=Groen

En we hebben twee diensten:

* Service A: met plaatsingsbeperking 'Kleur==Rood || Kleur==Blauw"
* Service B: met plaatsingsbeperking 'Kleur==Blauw || Kleur==Groen"

Hierdoor kan Service A op knooppunten 1 en 2 worden geplaatst en kan Service B op knooppunten 2 en 3 worden geplaatst.

In deze situatie bestaat de kans dat er een suboptimaal evenwicht wordt gemaakt.

Resource Manager herkent deze situatie en produceert een gezondheidsrapport waarin u wordt geadviseerd om een aantal services te splitsen.

Voor deze situatie kan de Resource Manager geen voorstel geven over het splitsen van de services, omdat meerdere splitsingen kunnen worden uitgevoerd en er geen manier is om in te schatten op welke manier de optimale manier zou zijn om de services te splitsen.

## <a name="configuring-subclustering"></a>Subclustering configureren

Het gedrag van Resource Manager over subclustering kan worden gewijzigd door de volgende configuratieparameters te wijzigen:
* SubclusteringEnabled - parameter bepaalt of Resource Manager rekening houdt met subclustering bij het maken van load balancing. Als deze parameter is uitgeschakeld, negeert Resource Manager subclustering en probeert het een optimaal evenwicht op mondiaal niveau te bereiken. De standaardwaarde van deze parameter is onjuist.
* SubclusteringReportingPolicy - bepaalt hoe Resourcemanager statusrapporten uitzendt voor hiërarchische en gedeeltelijke overlappingssubclustering. Een waarde van nul betekent dat gezondheidsrapporten over subclustering zijn uitgeschakeld, "1" betekent dat waarschuwingsstatusrapporten worden geproduceerd voor suboptimale subclusteringsituaties en dat een waarde van "2" "OK"-statusrapporten zal produceren. De standaardwaarde voor deze parameter is "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

via ClusterConfig.json voor zelfstandige implementaties of Template.json voor azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>Volgende stappen
* Als u wilt weten hoe de clusterresourcemanager de belasting in het cluster beheert en in evenwicht brengt, raadpleegt u het artikel over [het balanceren van de belasting](service-fabric-cluster-resource-manager-balancing.md)
* Zie [Knooppunteigenschappen en plaatsingsbeperkingen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) voor meer informatie over hoe uw services kunnen worden beperkt om alleen op bepaalde knooppunten te worden geplaatst

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
