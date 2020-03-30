---
title: Beschikbaarheid van Service Fabric-services
description: Beschrijft foutdetectie, failover en herstel van een service in een Azure Service Fabric-toepassing.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551859"
---
# <a name="availability-of-service-fabric-services"></a>Beschikbaarheid van Service Fabric-services
In dit artikel vindt u een overzicht van hoe Azure Service Fabric de beschikbaarheid van een service behoudt.

## <a name="availability-of-service-fabric-stateless-services"></a>Beschikbaarheid van servicefabric-statusloze services
Service Fabric-services kunnen stateful of stateloos zijn. Een stateless service is een applicatieservice die geen [lokale staat](service-fabric-concepts-state.md) heeft die zeer beschikbaar of betrouwbaar moet zijn.

Voor het maken van een `InstanceCount`statusloze service moet een . Het aantal instanties definieert het aantal exemplaren van de toepassingslogica van de statusloze service dat in het cluster moet worden uitgevoerd. Het verhogen van het aantal exemplaren is de aanbevolen manier om een stateless service uit te schalen.

Wanneer een instantie van een stateloze benoemde service mislukt, wordt een nieuw exemplaar gemaakt op een in aanmerking komend knooppunt in het cluster. Een stateless service-instantie kan bijvoorbeeld mislukken op Node1 en opnieuw worden gemaakt op Node5.

## <a name="availability-of-service-fabric-stateful-services"></a>Beschikbaarheid van servicefabric-stateful services
Een stateful service heeft een status die eraan is gekoppeld. In Service Fabric is een stateful service gemodelleerd als een set replica's. Elke replica is een lopende instantie van de code van de service. De replica heeft ook een kopie van de status voor die service. Lees- en schrijfbewerkingen worden uitgevoerd op één replica, de *naam Primair*. Wijzigingen in de status van schrijfbewerkingen *worden gerepliceerd* naar de andere replica's in de replicaset, *actieve secondaries*genoemd en toegepast. 

Er kan slechts één primaire replica zijn, maar er kunnen meerdere actieve secundaire replica's zijn. Het aantal actieve secundaire replica's is configureerbaar en een hoger aantal replica's kan een groter aantal gelijktijdige software- en hardwarefouten verdragen.

Als de primaire replica uitvalt, maakt Service Fabric een van de actieve secundaire replica's van de nieuwe primaire replica. Deze actieve secundaire replica heeft al de bijgewerkte versie van de status, via *replicatie,* en het kan doorgaan met het verwerken van verdere lees- en schrijfbewerkingen. Dit proces staat bekend als *herconfiguratie* en wordt verder beschreven in het artikel [Herconfiguratie.](service-fabric-concepts-reconfiguration.md)

Het concept van een replica is ofwel een primaire of actieve secundaire, staat bekend als de *replica rol*. Deze replica's worden verder beschreven in het artikel [Replica's en instanties.](service-fabric-concepts-replica-lifecycle.md) 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over servicefabric-concepten:

- [Services voor het schalen van servicefabric's](service-fabric-concepts-scalability.md)
- [Services voor partitioneringsservice](service-fabric-concepts-partitioning.md)
- [Status definiëren en beheren](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

