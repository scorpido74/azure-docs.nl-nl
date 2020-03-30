---
title: Gebeurtenisarchief Azure Service Fabric
description: Meer informatie over de EventStore van Azure Service Fabric, een manier om de status van een cluster of workloads op elk gewenst moment te begrijpen en te controleren.
author: srrengar
ms.topic: conceptual
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: d23c8114bf10ef3225775accef6910c0ba539e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645732"
---
# <a name="eventstore-overview"></a>Overzicht van EventStore

>[!NOTE]
>Vanaf Service Fabric versie 6.4. De EventStore-API's zijn alleen beschikbaar voor Windows-clusters die alleen op Azure worden uitgevoerd. We werken aan het porten van deze functionaliteit naar Linux en onze Standalone clusters.

## <a name="overview"></a>Overzicht

Geïntroduceerd in versie 6.2, de EventStore service is een monitoring optie in Service Fabric. EventStore biedt een manier om de status van uw cluster of workloads op een bepaald moment te begrijpen.
De EventStore is een stateful Service Fabric-service die gebeurtenissen uit het cluster bijhoudt. De gebeurtenis wordt weergegeven via de Service Fabric Explorer, REST en API's. EventStore stelt het cluster rechtstreeks op om diagnostische gegevens op een entiteit in uw cluster te verzamelen en moet worden gebruikt om te helpen:

* Problemen bij de ontwikkeling of het testen diagnosticeren of waar u mogelijk een bewakingspijplijn gebruikt
* Controleren of beheeracties die u op uw cluster onderneemt correct worden verwerkt
* Ontvang een momentopname van de manier waarop Service Fabric met een bepaalde entiteit omgaat

![EventStore (EventStore)](media/service-fabric-diagnostics-eventstore/eventstore.png)

Zie [Service Fabric-evenementen](service-fabric-diagnostics-event-generation-operational.md)voor een volledige lijst met evenementen die beschikbaar zijn in de EventStore.

>[!NOTE]
>Vanaf Service Fabric versie 6.4. De EventStore API's en UX zijn over het algemeen beschikbaar voor Azure Windows-clusters. We werken aan het porten van deze functionaliteit naar Linux en onze Standalone clusters.

De EventStore-service kan worden opgevraagd voor gebeurtenissen die beschikbaar zijn voor elke entiteit en entiteitstype in uw cluster. Dit betekent dat u zoeken naar gebeurtenissen op de volgende niveaus:
* Cluster: gebeurtenissen die specifiek zijn voor het cluster zelf (bijv. clusterupgrade)
* Knooppunten: alle nodeniveaugebeurtenissen
* Knooppunt: gebeurtenissen die specifiek zijn voor één knooppunt, geïdentificeerd door`nodeName`
* Toepassingen: alle gebeurtenissen op toepassingsniveau
* Toepassing: gebeurtenissen die specifiek zijn voor één toepassing die is geïdentificeerd door`applicationId`
* Services: gebeurtenissen van alle services in uw clusters
* Service: gebeurtenissen van een specifieke service die is geïdentificeerd door`serviceId`
* Partities: gebeurtenissen van alle partities
* Partitie: gebeurtenissen van een specifieke partitie die is geïdentificeerd door`partitionId`
* Partitiereplica's: gebeurtenissen van alle replica's/ instanties binnen een specifieke partitie die is geïdentificeerd door`partitionId`
* Partitiereplica: gebeurtenissen van een specifieke replica/instantie die door `replicaId` en`partitionId`

Ga voor meer informatie over de API naar de [GebeurtenisStore API-referentie](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

De EventStore-service heeft ook de mogelijkheid om gebeurtenissen in uw cluster te correleren. Door te kijken naar gebeurtenissen die tegelijkertijd zijn geschreven van verschillende entiteiten die mogelijk van invloed zijn op elkaar, kan de EventStore-service deze gebeurtenissen koppelen om te helpen bij het identificeren van oorzaken voor activiteiten in uw cluster. Als een van uw toepassingen bijvoorbeeld ongezond wordt zonder dat er een reactie wordt aangebracht, kijkt de EventStore `Error` ook `Warning` naar andere gebeurtenissen die door het platform worden blootgesteld en kan dit correleren met een of gebeurtenis. Dit helpt bij snellere foutdetectie en analyse van de onderliggende oorzaken.

## <a name="enable-eventstore-on-your-cluster"></a>EventStore inschakelen in uw cluster

### <a name="local-cluster"></a>Lokaal cluster

Voeg in [fabricSettings.json in uw cluster](service-fabric-cluster-fabric-settings.md)EventStoreService toe als addOn-functie en voer een clusterupgrade uit.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Azure-clusterversie 6.5+
Als uw Azure-cluster wordt geüpgraded naar versie 6.5 of hoger, wordt EventStore automatisch ingeschakeld op uw cluster. Als u zich wilt afmelden, moet u uw clustersjabloon als volgt bijwerken:

* Een API-versie `2019-03-01` van of nieuwer gebruiken 
* De volgende code toevoegen aan de sectie Eigenschappen in uw cluster
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Azure-clusterversie 6.4

Als u versie 6.4 gebruikt, u de sjabloon Azure Resource Manager bewerken om de EventStore-service in te schakelen. Dit wordt gedaan door een [upgrade van clusterconfig](service-fabric-cluster-config-upgrade-azure.md) uit te voeren en de volgende code toe te voegen, u Plaatsingsbeperkingen gebruiken om de replica's van de EventStore-service op een specifiek NodeType te plaatsen, bijvoorbeeld een NodeType dat is toegewezen aan de systeemservices. De `upgradeDescription` sectie configureert de config-upgrade om een herstart op de knooppunten te activeren. U de sectie in een andere update verwijderen.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Volgende stappen
* Aan de slag met de EventStore API - [De EventStore-API's gebruiken in Azure Service Fabric-clusters](service-fabric-diagnostics-eventstore-query.md)
* Meer informatie over de lijst met evenementen die worden aangeboden door EventStore - [Service Fabric-evenementen](service-fabric-diagnostics-event-generation-operational.md)
* Overzicht van monitoring en diagnostiek in Service Fabric - [Monitoring en Diagnostiek voor Service Fabric](service-fabric-diagnostics-overview.md)
* Bekijk de volledige lijst met API-aanroepen - [EventStore REST API-referentie](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* Meer informatie over het bewaken van uw cluster - [Bewaken van het cluster en platform](service-fabric-diagnostics-event-generation-infra.md).
