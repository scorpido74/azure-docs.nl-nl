---
title: Prestatiebewaking azure-servicefabric
description: Meer informatie over prestatiemeteritems voor het bewaken en diagnosticeren van Azure Service Fabric-clusters.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464697"
---
# <a name="performance-metrics"></a>Metrische gegevens voor prestaties

Statistieken moeten worden verzameld om inzicht te krijgen in de prestaties van uw cluster en de toepassingen die erin worden uitgevoerd. Voor servicefabricclusters raden we aan de volgende prestatiemeteritems te verzamelen.

## <a name="nodes"></a>Knooppunten

Voor de machines in uw cluster u de volgende prestatiemeteritems verzamelen om de belasting van elke machine beter te begrijpen en de juiste beslissingen over clusterschaling te nemen.

| Tellercategorie | Tellernaam |
| --- | --- |
| Logische schijf | Logische schijfvrije ruimte |
| PhysicalDisk(per schijf) | De wachtrijlengte van de schijfleeswachtrij |
| PhysicalDisk(per schijf) | Avg. Wachtrijlengte voor schrijfschijven |
| PhysicalDisk(per schijf) | Avg. Schijf sec/read |
| PhysicalDisk(per schijf) | Avg. Schijf sec/write |
| PhysicalDisk(per schijf) | Schijfleest/sec |
| PhysicalDisk(per schijf) | Bytes voor schijflezen per seconde |
| PhysicalDisk(per schijf) | Schijfschrijft/sec |
| PhysicalDisk(per schijf) | Bytes voor schijfschrijfschrijfberichten per seconde |
| Geheugen | Beschikbare mbytes |
| PagingFile | % Gebruik |
| Processor(Totaal) | Percentage processortijd |
| Proces (per service) | Percentage processortijd |
| Proces (per service) | ID-proces |
| Proces (per service) | Privébytes |
| Proces (per service) | Aantal draad |
| Proces (per service) | Virtuele bytes |
| Proces (per service) | Werkset |
| Proces (per service) | Werkset - Privé |
| Netwerkinterface(alle exemplaren) | Bytes recd |
| Netwerkinterface(alle exemplaren) | Verzonden bytes |
| Netwerkinterface(alle exemplaren) | Totaal aantal bytes |
| Netwerkinterface(alle exemplaren) | Uitvoerwachtrijlengte |
| Netwerkinterface(alle exemplaren) | Pakketten uitgaand weggegooid |
| Netwerkinterface(alle exemplaren) | Ontvangen pakketten verwijderd |
| Netwerkinterface(alle exemplaren) | Pakketten Uitgaande fouten |
| Netwerkinterface(alle exemplaren) | Fouten ontvangen pakketten |

## <a name="net-applications-and-services"></a>.NET-toepassingen en -diensten

Verzamel de volgende tellers als u .NET-services implementeert in uw cluster. 

| Tellercategorie | Tellernaam |
| --- | --- |
| .NET CLR-geheugen (per service) | Proces-id |
| .NET CLR-geheugen (per service) | # Totaal vastgelegde bytes |
| .NET CLR-geheugen (per service) | Aantal aantal gereserveerde bytes |
| .NET CLR-geheugen (per service) | # Bytes in alle heaps |
| .NET CLR-geheugen (per service) | Grote grootte van de objectheap |
| .NET CLR-geheugen (per service) | # GC Handgrepen |
| .NET CLR-geheugen (per service) | # Gen 0 Collecties |
| .NET CLR-geheugen (per service) | # Gen 1 Collecties |
| .NET CLR-geheugen (per service) | # Gen 2 Collecties |
| .NET CLR-geheugen (per service) | % Tijd in GC |

### <a name="service-fabrics-custom-performance-counters"></a>De aangepaste prestatiemeteritems van Service Fabric

Service Fabric genereert een aanzienlijke hoeveelheid aangepaste prestatiemeteritems. Als u de SDK hebt geïnstalleerd, ziet u de uitgebreide lijst op uw Windows-machine in uw prestatiemonitor (Start > Prestatiemonitor). 

In de toepassingen die u implementeert in uw cluster, als `Service Fabric Actor` `Service Fabric Actor Method` u betrouwbare actoren gebruikt, voeg tellers toe en categorieën (zie [Service Fabric Reliable Actors Diagnostics).](service-fabric-reliable-actors-diagnostics.md)

Als u betrouwbare services of serviceremoting `Service Fabric Service` gebruikt, hebben en `Service Fabric Service Method` tellercategorieën waarvan u tellers moet verzamelen, zie [bewaking met serviceremoting](service-fabric-reliable-serviceremoting-diagnostics.md) en betrouwbare [serviceprestatiemeter .](service-fabric-reliable-services-diagnostics.md#performance-counters) 

Als u betrouwbare verzamelingen gebruikt, raden we u aan de `Avg. Transaction ms/Commit` gemiddelde commitlatentie per transactiestatistiek `Service Fabric Transactional Replicator` toe te voegen.


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het genereren van evenementen op platformniveau](service-fabric-diagnostics-event-generation-infra.md) in Service Fabric
* Prestatiestatistieken verzamelen via [de Log Analytics-agent](service-fabric-diagnostics-oms-agent.md)
