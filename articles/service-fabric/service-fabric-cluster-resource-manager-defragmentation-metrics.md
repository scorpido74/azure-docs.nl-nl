---
title: Defragmentatie van metrische gegevens in Azure Service Fabric
description: Meer informatie over het gebruik van defragmentatie of verpakking als strategie voor statistieken in Service Fabric. Deze techniek is handig voor zeer grote diensten.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bba459be4408f4a4bc438bb33b0570a91e84f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563357"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentatie van statistieken en belasting in Service Fabric
De standaardstrategie van de servicefabricclusterbronmanager voor het beheren van belastingsstatistieken in het cluster is het distribueren van de belasting. Ervoor zorgen dat knooppunten gelijkmatig worden gebruikt vermijdt warme en koude plekken die leiden tot zowel geschil en verspilde middelen. Het distribueren van workloads in het cluster is ook de veiligste in termen van overlevende fouten, omdat het ervoor zorgt dat een fout niet een groot percentage van een bepaalde werkbelasting. 

De Service Fabric Cluster Resource Manager ondersteunt wel een andere strategie voor het beheren van belasting, wat defragmentatie is. Defragmentatie betekent dat in plaats van te proberen het gebruik van een statistiek over het cluster te verdelen, deze wordt geconsolideerd. Consolidatie is slechts een omkering van de standaardbalanceringsstrategie - in plaats van de gemiddelde standaarddeviatie van metrische belasting te minimaliseren, probeert de Cluster Resource Manager deze te verhogen.

## <a name="when-to-use-defragmentation"></a>Wanneer defragmentatie gebruiken
Als u de belasting in het cluster distribueert, worden een aantal resources op elk knooppunt verbruikt. Sommige workloads maken services die uitzonderlijk groot zijn en de meeste of alle node verbruiken. In deze gevallen is het mogelijk dat wanneer er grote workloads worden gemaakt dat er niet genoeg ruimte is op een knooppunt om ze uit te voeren. Grote workloads zijn geen probleem in Service Fabric; In deze gevallen bepaalt de Clusterresourcemanager dat het cluster moet reorganiseren om ruimte te maken voor deze grote werkbelasting. In de tussentijd moet die werkbelasting echter wachten om in het cluster te worden gepland.

Als er veel services en status om te bewegen, dan kan het lang duren voor de grote werkbelasting te worden geplaatst in het cluster. Dit is waarschijnlijker als andere workloads in het cluster ook groot zijn en dus langer duren om te reorganiseren. Het Service Fabric-team heeft de creatietijden gemeten in simulaties van dit scenario. We ontdekten dat het maken van grote services veel langer duurde zodra het clustergebruik boven de 30% en 50% lag. Om dit scenario aan te pakken, introduceerden we defragmentatie als een evenwichtsstrategie. We ontdekten dat voor grote workloads, vooral die waar de creatietijd belangrijk was, defragmentatie echt hielp die nieuwe workloads krijgen gepland in het cluster.

U defragmentatiestatistieken configureren om de Cluster Resource Manager te hebben om proactief te proberen de belasting van de services te condenseren in minder knooppunten. Dit helpt ervoor te zorgen dat er bijna altijd ruimte is voor grote diensten zonder het cluster te reorganiseren. Als u het cluster niet hoeft te reorganiseren, kunnen snel grote workloads worden gemaakt.

De meeste mensen hebben geen defragmentatie nodig. Diensten zijn meestal klein, dus het is niet moeilijk om ruimte voor hen te vinden in het cluster. Wanneer reorganisatie mogelijk is, gaat het snel, opnieuw omdat de meeste diensten klein zijn en snel en parallel kunnen worden verplaatst. Echter, als je grote diensten en moet ze snel gemaakt dan is de defragmentatie strategie is voor jou. We bespreken de afwegingen van het gebruik van defragmentatie volgende. 

## <a name="defragmentation-tradeoffs"></a>Defragmentatie afwegingen
Defragmentatie kan de impactvan storingen vergroten, omdat er meer services worden uitgevoerd op knooppunten die mislukken. Defragmentatie kan ook de kosten verhogen, omdat resources in het cluster in reserve moeten worden gehouden, in afwachting van het creëren van grote workloads.

Het volgende diagram geeft een visuele weergave van twee clusters, een die is gedefragmenteerd en een die niet is. 

<center>

![Evenwichtige en gedefragmenteerde clusters vergelijken][Image1]
</center>

Houd in het evenwichtige geval rekening met het aantal bewegingen dat nodig zou zijn om een van de grootste serviceobjecten te plaatsen. In het gedefragmenteerde cluster kan de grote werkbelasting op knooppunten vier of vijf worden geplaatst zonder te hoeven wachten tot andere services worden verplaatst.

## <a name="defragmentation-pros-and-cons"></a>Defragmentatie voor- en nadelen
Dus wat zijn die andere conceptuele afwegingen? Hier is een snelle tabel met dingen om over na te denken:

| Defragmentatie Pro's | Defragmentatie Nadelen |
| --- | --- |
| Maakt snellere creatie van grote diensten mogelijk |Concentreert belasting op minder knooppunten, waardoor de twist toeneemt |
| Maakt minder gegevensverplaatsing mogelijk tijdens het maken |Storingen kunnen van invloed zijn op meer services en leiden tot meer verloop |
| Maakt een rijke beschrijving van de eisen en terugwinning van de ruimte |Complexere algemene resourcebeheerconfiguratie |

U gedefragmenteerde en normale statistieken in hetzelfde cluster mixen. De Cluster Resource Manager probeert de defragmentatie statistieken zoveel mogelijk te consolideren, terwijl het verspreiden van de anderen. De resultaten van het mengen van defragmentatie- en balanceringsstrategieën zijn afhankelijk van verschillende factoren, waaronder:
  - het aantal balanceringsstatistieken versus het aantal defragmentatiestatistieken
  - Of een service beide typen statistieken gebruikt 
  - de metrische gewichten
  - huidige metrische belastingen
  
Experimenten zijn vereist om de exacte configuratie te bepalen die nodig is. We raden u aan uw workloads grondig te meten voordat u defragmentatiestatistieken in productie inschakelt. Dit geldt vooral bij het mengen van defragmentatie en gebalanceerde metrics binnen dezelfde service. 

## <a name="configuring-defragmentation-metrics"></a>Defragmentatiestatistieken configureren
Het configureren van defragmentatiestatistieken is een algemene beslissing in het cluster en individuele statistieken kunnen worden geselecteerd voor defragmentatie. In de volgende config-fragmenten ziet u hoe u metrische gegevens configureert voor defragmentatie. In dit geval is 'Metric1' geconfigureerd als een defragmentatiestatistiek, terwijl 'Metric2' normaal in evenwicht blijft. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

via ClusterConfig.json voor zelfstandige implementaties of Template.json voor azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Volgende stappen
- De Cluster Resource Manager heeft man opties voor het beschrijven van het cluster. Voor meer informatie over hen, check out dit artikel over [het beschrijven van een Service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md)
- Statistieken zijn hoe de Clusterresourcemanger van de servicestructuur het verbruik en de capaciteit in het cluster beheert. Bekijk [dit artikel](service-fabric-cluster-resource-manager-metrics.md) voor meer informatie over statistieken en hoe u deze configureren

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
