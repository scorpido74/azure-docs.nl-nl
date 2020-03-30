---
title: Beperking in de clusterbronbeheer servicefabric
description: Leer de gashendels van de clusterbronbeheer van servicestructuur configureren.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452165"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Beperking van de clusterbronbeheer voor servicefabric
Zelfs als u de clusterbronbeheer correct hebt geconfigureerd, kan het cluster worden verstoord. Er kunnen bijvoorbeeld gelijktijdige node- en foutdomeinfouten zijn - wat zou er gebeuren als dat gebeurde tijdens een upgrade? De Clusterresourcemanager probeert altijd alles op te lossen en verbruikt de bronnen van het cluster om het cluster te reorganiseren en op te lossen. Throttles helpen een backstop te bieden, zodat het cluster resources kan gebruiken om te stabiliseren - de knooppunten komen terug, de netwerkpartities genezen, gecorrigeerde bits worden geïmplementeerd.

Om te helpen met dit soort situaties, bevat de Service Fabric Cluster Resource Manager verschillende throttles. Deze gashendels zijn allemaal vrij grote hamers. Over het algemeen moeten ze niet worden veranderd zonder zorgvuldige planning en testen.

Als u de gashendels van clusterbronbeheer wijzigt, moet u deze afstemmen op de verwachte werkelijke belasting. U bepalen dat u wat gashendels moet hebben, zelfs als dit betekent dat het cluster in sommige situaties langer duurt om te stabiliseren. Testen is vereist om de juiste waarden voor gashendels te bepalen. Throttles moeten hoog genoeg zijn om het cluster in staat te stellen te reageren op veranderingen in een redelijke hoeveelheid tijd, en laag genoeg om daadwerkelijk te veel resourceverbruik te voorkomen. 

De meeste van de tijd hebben we gezien klanten gebruik gashendels het is omdat ze al in een resource beperkte omgeving. Enkele voorbeelden zijn een beperkte netwerkbandbreedte voor afzonderlijke knooppunten of schijven die niet in staat zijn om veel stateful replica's parallel te bouwen vanwege doorvoerbeperkingen. Zonder gashendels kunnen bewerkingen deze resources overweldigen, waardoor bewerkingen mislukken of traag zijn. In deze situaties gebruikten klanten gashendels en wisten ze dat ze de tijd die het cluster nodig had om een stabiele toestand te bereiken, uit te breiden. Klanten begrepen ook dat ze konden eindigen draaien op een lagere algehele betrouwbaarheid, terwijl ze werden gewurgd.


## <a name="configuring-the-throttles"></a>De gashendels configureren

Service Fabric heeft twee mechanismen voor het beperken van het aantal replicabewegingen. Het standaardmechanisme dat bestond vóór Service Fabric 5.7 vertegenwoordigt beperking als een absoluut aantal toegestane zetten. Dit werkt niet voor clusters van alle groottes. Met name voor grote clusters kan de standaardwaarde te klein zijn, waardoor de balancering aanzienlijk wordt vertraagd, zelfs wanneer dit nodig is, terwijl deze geen effect heeft in kleinere clusters. Dit eerdere mechanisme is vervangen door op percentage gebaseerde beperking, die beter schaalt met dynamische clusters waarin het aantal services en knooppunten regelmatig verandert.

De gashendels zijn gebaseerd op een percentage van het aantal replica's in de clusters. Op percentage gebaseerde gashendels maken het mogelijk om de regel uit te drukken: "verplaats niet meer dan 10% van de replica's in een interval van 10 minuten", bijvoorbeeld.

De configuratie-instellingen voor op percentage gebaseerde beperking zijn:

  - GlobalMovementThrottleThresholdPercentage - Maximaal aantal bewegingen toegestaan in cluster op elk gewenst moment, uitgedrukt als percentage van het totale aantal replica's in het cluster. 0 geeft geen limiet aan. De standaardwaarde is 0. Als zowel deze instelling als GlobalMovementThrottleThreshold zijn opgegeven, wordt de conservatievere limiet gebruikt.
  - GlobalMovementThrottleThresholdPercentageForPlacement - Maximaal aantal bewegingen toegestaan tijdens de plaatsingsfase, uitgedrukt als percentage van het totale aantal replica's in het cluster. 0 geeft geen limiet aan. De standaardwaarde is 0. Als zowel deze instelling als GlobalMovementThrottleThresholdForPlacement zijn opgegeven, wordt de conservatievere limiet gebruikt.
  - GlobalMovementThrottleThresholdPercentageForBalancing - Maximaal aantal bewegingen toegestaan tijdens de balanceringsfase, uitgedrukt als percentage van het totale aantal replica's in het cluster. 0 geeft geen limiet aan. De standaardwaarde is 0. Als zowel deze instelling als GlobalMovementThrottleThresholdForBalancing zijn opgegeven, wordt de meer conservatieve limiet gebruikt.

Wanneer u het gaspedaalpercentage opgeeft, geeft u 5% op als 0,05. Het interval waarop deze gashendels worden geregeld, is het GlobalMovementThrottleCountingInterval, dat in seconden is opgegeven.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

via ClusterConfig.json voor zelfstandige implementaties of Template.json voor azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Op standaardtelling gebaseerde gashendels
Deze informatie wordt verstrekt in het geval u oudere clusters hebt of deze configuraties nog steeds behoudt in clusters die sindsdien zijn bijgewerkt. In het algemeen wordt aanbevolen deze te vervangen door de bovenstaande gaspedaaladoordoordoordoor% Aangezien op percentage gebaseerde beperking standaard is uitgeschakeld, blijven deze gashendels de standaardgashendels voor een cluster totdat ze zijn uitgeschakeld en worden vervangen door de op percentage gebaseerde gashendels. 

  - GlobalMovementThrottleThreshold – deze instelling regelt het totale aantal bewegingen in het cluster gedurende enige tijd. De hoeveelheid tijd wordt in enkele seconden opgegeven als de GlobalMovementThrottleCountingInterval. De standaardwaarde voor de GlobalMovementThrottleThreshold is 1000 en de standaardwaarde voor het GlobalMovementThrottleCountingInterval is 600.
  - MovementPerPartitionThrottleThreshold – deze instelling regelt het totale aantal bewegingen voor een servicepartitie gedurende enige tijd. De hoeveelheid tijd wordt in enkele seconden opgegeven als de MovementPerPartitionThrottleCountingInterval. De standaardwaarde voor de MovementPerPartitionThrottleThreshold is 50 en de standaardwaarde voor de Interval van het papierperpartitie./uur is 600.

De configuratie voor deze gashendels volgt hetzelfde patroon als de op percentage gebaseerde beperking.

## <a name="next-steps"></a>Volgende stappen
- Als u wilt weten hoe de clusterresourcemanager de belasting in het cluster beheert en in evenwicht brengt, raadpleegt u het artikel over [het balanceren van de belasting](service-fabric-cluster-resource-manager-balancing.md)
- De Clusterresourcemanager heeft veel opties voor het beschrijven van het cluster. Voor meer informatie over hen, check out dit artikel over [het beschrijven van een Service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md)
