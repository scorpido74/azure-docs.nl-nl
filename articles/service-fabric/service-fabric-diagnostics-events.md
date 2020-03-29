---
title: Azure-servicefabricgebeurtenissen
description: Lees meer over de out-of-the-box geleverde Service Fabric-gebeurtenissen waarmee u uw Azure Service Fabric-cluster bewaken.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 638b650e485ad3e83bd6021639a7e55b540d9cdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451730"
---
# <a name="service-fabric-events"></a>Service Fabric-gebeurtenissen 

Het Service Fabric-platform schrijft verschillende gestructureerde gebeurtenissen voor belangrijke operationele activiteiten binnen uw cluster. Deze variëren van clusterupgrades tot beslissingen over het plaatsen van replica's. Elke gebeurtenis die Service Fabric kaarten blootstelt aan een van de volgende entiteiten in het cluster:
* Cluster
* Toepassing
* Service
* Partitie
* Replica 
* Container

Om een volledige lijst te zien van gebeurtenissen die door het platform worden weergegeven - [Lijst met servicefabric-gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md).

Hier volgen enkele voorbeelden van scenario's waarvoor u gebeurtenissen in uw cluster moet zien. 
* Node lifecycle events: as nodes come up, go down, scale in/out, restart, and are activated/deactivated, these events will be exposed you what happened, and help you identify if there's wrong with the machine itself or if there was a API that was called through SF to modify the status of a node.
* Clusterupgrade: als uw cluster is bijgewerkt (SF-versie of configuratiewijziging), ziet u de upgrade-e-it-initiatief, rol door elk van uw upgradedomeinen en voltooit (of terugschakelen). 
* Toepassingsupgrades: net als clusterupgrades is er een uitgebreide reeks gebeurtenissen terwijl de upgrade doorloopt. Deze gebeurtenissen kunnen handig zijn om te begrijpen wanneer een upgrade is gepland, de huidige status van een upgrade en de algehele volgorde van gebeurtenissen. Dit is handig om terug te kijken om te zien welke upgrades met succes zijn uitgerold of dat er een rollback is geactiveerd.
* Implementatie/verwijdering van toepassing/service: er zijn gebeurtenissen voor elke toepassing, service en container, die worden gemaakt of verwijderd en nuttig zijn bij het in- of uitschalen, bijvoorbeeld bij het verhogen van het aantal replica's
* Partitie wordt verplaatst (herconfiguratie): wanneer een stateful partitie door een herconfiguratie gaat (een wijziging in de replicaset), wordt een gebeurtenis geregistreerd. Dit is handig als u probeert te begrijpen hoe vaak uw partitiereplicaset wordt gewijzigd of mislukt, of als u bijhoudt welk knooppunt uw primaire replica op elk moment heeft uitgevoerd.
* Chaos-gebeurtenissen: wanneer u de [Chaos-service](service-fabric-controlled-chaos.md) van Service Fabric gebruikt, ziet u gebeurtenissen elke keer dat de service wordt gestart of gestopt, of wanneer deze een fout in het systeem injecteert.
* Statusgebeurtenissen: Service fabric stelt statusgebeurtenissen bloot telkens wanneer een waarschuwing of een foutstatusrapport wordt gemaakt of een entiteit teruggaat naar een OK-status of een statusrapport verloopt. Deze gebeurtenissen zijn zeer nuttig om historische gezondheidsstatistieken voor een entiteit bij te houden. 

## <a name="how-to-access-events"></a>Toegang tot evenementen

Er zijn een paar verschillende manieren waarop Service Fabric-evenementen toegankelijk zijn:
* De gebeurtenissen worden vastgelegd via standaardkanalen zoals ETW/Windows Event-logboeken en kunnen worden gevisualiseerd door een bewakingstool die deze ondersteunt, zoals Azure Monitor-logboeken. Standaard hebben clusters die in de portal zijn gemaakt, diagnose ingeschakeld en worden de Windows Azure-diagnoseagent de gebeurtenissen naar Azure-tabelopslag verzonden, maar u moet dit nog steeds integreren met uw logboekanalysebron. Lees meer over het configureren van de [Azure Diagnostics-agent](service-fabric-diagnostics-event-aggregation-wad.md) om de diagnostische configuratie van uw cluster te wijzigen om meer logboeken of prestatiemeteritems op te pikken en de [Azure Monitor-logboekenintegratie](service-fabric-diagnostics-event-analysis-oms.md)
* De Rest-API's van de Rest van EventStore-service waarmee u het cluster rechtstreeks of via de Clientbibliotheek van de ServiceFabric opvragen. Zie [Query EventStore API's voor clustergebeurtenissen](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het monitoren van uw cluster - [Monitoring van het cluster en platform](service-fabric-diagnostics-event-generation-infra.md).
* Meer informatie over de EventStore-service - [EventStore-serviceoverzicht](service-fabric-diagnostics-eventstore.md)
