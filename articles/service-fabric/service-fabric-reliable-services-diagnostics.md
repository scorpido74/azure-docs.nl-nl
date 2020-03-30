---
title: Diagnose van Azure Service Fabric Stateful Reliable Services
description: Diagnostische functionaliteit voor Stateful Reliable Services in Azure Service Fabric
author: dkkapur
ms.topic: conceptual
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: 37162287e130b05dc41453c579b3a628ac878fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282261"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnosefunctionaliteit voor Stateful Reliable Services
De klasse Azure Service Fabric Stateful Reliable Services StatefulServiceBase zendt [EventSource-gebeurtenissen](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) uit die kunnen worden gebruikt om de service te debuggen, inzicht te geven in hoe de runtime werkt en hulp bij het oplossen van problemen.

## <a name="eventsource-events"></a>EventSource-gebeurtenissen
De naam EventSource voor de Stateful Reliable Services StatefulServiceBase-klasse is 'Microsoft-ServiceFabric-Services'. Gebeurtenissen van deze gebeurtenisbron worden weergegeven in het venster [Diagnostische gebeurtenissen](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) wanneer de service wordt verwijderd in [Visual Studio](service-fabric-debugging-your-application.md).

Voorbeelden van hulpprogramma's en technologieën die helpen bij het verzamelen en/of bekijken van EventSource-gebeurtenissen zijn [PerfView,](https://www.microsoft.com/download/details.aspx?id=28567) [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)en de [Microsoft TraceEvent-bibliotheek](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Gebeurtenissen
| Gebeurtenisnaam | Gebeurtenis-id | Niveau | Gebeurtenisbeschrijving |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informatief |Uitgestoten wanneer de taak RunAsync van de service wordt gestart |
| StatefulRunAsyncAnnulering |2 |Informatief |Wordt uitgestoten wanneer de taak RunAsync van de service wordt geannuleerd |
| StatefulRunAsyncCompletion |3 |Informatief |Uitgestoten wanneer de taak RunAsync van de service is voltooid |
| StatefulRunAsyncSlowCancellation |4 |Waarschuwing |Uitgestoten wanneer de runasync-taak van de service te lang duurt om de annulering te voltooien |
| StatefulRunAsyncFailure |5 |Fout |Uitgestoten wanneer de taak RunAsync van de service een uitzondering uitgooit |

## <a name="interpret-events"></a>Gebeurtenissen interpreteren
StatefulRunAsyncInvocation, StatefulRunAsyncCompletion en StatefulRunAsyncCancellation-gebeurtenissen zijn nuttig voor de serviceschrijver om de levenscyclus van een service te begrijpen, evenals de timing voor wanneer een service wordt gestart, geannuleerd of voltooid. Deze informatie kan handig zijn bij het opsporen van serviceproblemen of het begrijpen van de levenscyclus van de service.

Serviceschrijvers moeten veel aandacht besteden aan StatefulRunAsyncSlowCancellation en StatefulRunAsyncFailure-gebeurtenissen omdat ze problemen met de service aangeven.

StatefulRunAsyncFailure wordt uitgezonden wanneer de taak RunAsync() van de service een uitzondering maakt. Een met een uitzondering gegooide duidt meestal op een fout of fout in de service. Bovendien zorgt de uitzondering ervoor dat de service mislukt, zodat deze naar een ander knooppunt wordt verplaatst. Deze bewerking kan duur zijn en kan binnenkomende aanvragen vertragen terwijl de service wordt verplaatst. Serviceschrijvers moeten de oorzaak van de uitzondering bepalen en, indien mogelijk, deze beperken.

StatefulRunAsyncSlowCancellation wordt weergegeven wanneer een annuleringsverzoek voor de RunAsync-taak langer dan vier seconden duurt. Wanneer het te lang duurt voordat een service wordt geannuleerd, wordt de service mogelijk gemaakt om snel opnieuw te worden gestart op een ander knooppunt. Dit scenario kan van invloed zijn op de algehele beschikbaarheid van de service.

## <a name="performance-counters"></a>Prestatiemeteritems
De runtime van Reliable Services definieert de volgende prestatiemetercategorieën:

| Categorie | Beschrijving |
| --- | --- |
| Transactionele replicator van servicefabric |Tellers die specifiek zijn voor de Transactionele replicator van Azure-servicefabric |
| Service Fabric TStore |Tellers die specifiek zijn voor de Azure Service Fabric TStore |

De Transactionele Replicator voor servicefabric wordt door de [Betrouwbare Staatsmanager](service-fabric-reliable-services-reliable-collections-internals.md) gebruikt om transacties binnen een bepaalde set [replica's](service-fabric-concepts-replica-lifecycle.md)te repliceren.

De Service Fabric TStore is een onderdeel dat wordt gebruikt in [betrouwbare collecties](service-fabric-reliable-services-reliable-collections-internals.md) voor het opslaan en ophalen van sleutelwaardeparen.

De [Windows Performance Monitor-toepassing](https://technet.microsoft.com/library/cc749249.aspx) die standaard beschikbaar is in het Windows-besturingssysteem, kan worden gebruikt om prestatiemetergegevens te verzamelen en weer te geven. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) is een andere optie voor het verzamelen van prestatiemetergegevens en het uploaden naar Azure-tabellen.

### <a name="performance-counter-instance-names"></a>Instantienamen voor prestatiemeter
Een cluster met een groot aantal betrouwbare services of betrouwbare servicepartities heeft een groot aantal transactionele replicatorprestatiemeterexemplaren. Dit is ook het geval voor TStore-prestatiemeteritems, maar wordt ook vermenigvuldigd met het aantal gebruikte betrouwbare woordenboeken en betrouwbare wachtrijen. De namen van de prestatietellerinstantie kunnen helpen bij het identificeren van de specifieke [partitie,](service-fabric-concepts-partitioning.md)servicereplica en overheidsprovider in het geval van TStore, waarmee de instantie van de prestatieteller is gekoppeld.

#### <a name="service-fabric-transactional-replicator-category"></a>Categorie Transactionele replicator servicefabric
Voor de `Service Fabric Transactional Replicator`categorie bevinden de namen van de tellerinstantie zich in de volgende indeling:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* is de tekenreeksweergave van de servicefabricpartitie-id waarmee de instantie van de prestatieteller is gekoppeld. De partitie-ID is een GUID en [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) de tekenreeksweergave wordt gegenereerd door met indelingsaanduiding "D".

*ServiceFabricReplicaId* is de ID die is gekoppeld aan een bepaalde replica van een betrouwbare service. De replica-id is opgenomen in de naam van de instantie van de prestatiemeter om de uniciteit ervan te garanderen en conflicten met andere prestatiemeterexemplaren die door dezelfde partitie worden gegenereerd, te voorkomen. Verdere details over replica's en hun rol in betrouwbare diensten vindt u [hier.](service-fabric-concepts-replica-lifecycle.md)

De volgende naam van de tegeninstantie `Service Fabric Transactional Replicator` is typisch voor een teller onder de categorie:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

In het voorgaande `00d0126d-3e36-4d68-98da-cc4f7195d85e` voorbeeld is de tekenreeksweergave van `131652217797162571` de partitie-id van de servicestructuur en de replica-id.

#### <a name="service-fabric-tstore-category"></a>Categorie Service Fabric TStore
Voor de `Service Fabric TStore`categorie bevinden de namen van de tellerinstantie zich in de volgende indeling:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* is de tekenreeksweergave van de servicefabricpartitie-id waarmee de instantie van de prestatieteller is gekoppeld. De partitie-ID is een GUID en [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) de tekenreeksweergave wordt gegenereerd door met indelingsaanduiding "D".

*ServiceFabricReplicaId* is de ID die is gekoppeld aan een bepaalde replica van een betrouwbare service. De replica-id is opgenomen in de naam van de instantie van de prestatiemeter om de uniciteit ervan te garanderen en conflicten met andere prestatiemeterexemplaren die door dezelfde partitie worden gegenereerd, te voorkomen. Verdere details over replica's en hun rol in betrouwbare diensten vindt u [hier.](service-fabric-concepts-replica-lifecycle.md)

*StateProviderId* is de ID die is gekoppeld aan een overheidsprovider binnen een betrouwbare service. De statusprovider-id is opgenomen in de naam van de prestatietellerom een TStore van een andere te onderscheiden.

*PerformanceCounterInstanceDifferentiator* is een onderscheidende ID die is gekoppeld aan een prestatietellerinstantie binnen een overheidsprovider. Deze differentiator is opgenomen in de naam van de instantie van de prestatieteller om de uniciteit ervan te garanderen en conflicten met andere prestatiemeterexemplaren die door dezelfde statusprovider worden gegenereerd, te voorkomen.

*StateProviderName* is de naam die is gekoppeld aan een overheidsprovider binnen een betrouwbare service. De naam van de statusprovider is opgenomen in de naam van de instantie van de prestatiemeter voor gebruikers om eenvoudig te bepalen welke status deze biedt.

De volgende naam van de tegeninstantie `Service Fabric TStore` is typisch voor een teller onder de categorie:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

In het voorgaande `00d0126d-3e36-4d68-98da-cc4f7195d85e` voorbeeld is de tekenreeksweergave van `131652217797162571` de servicefabricpartitie-id, is de replica-id, `142652217797162571` is de statusprovider-ID en `1337` is de instantiedifferentiator van de prestatieteller. `urn:MyReliableDictionary/dataStore`is de naam van de overheidsprovider die `urn:MyReliableDictionary`gegevens opslaat voor de verzameling met de naam .

### <a name="transactional-replicator-performance-counters"></a>Transactionele Replicator-prestatiemeteritems

De runtime van Reliable Services zendt `Service Fabric Transactional Replicator` de volgende gebeurtenissen uit onder de categorie

 Naam van het meteritem | Beschrijving |
| --- | --- |
| Txn Operations/sec starten | Het aantal nieuwe schrijftransacties dat per seconde wordt gemaakt.|
| Txn Operations/sec | Het aantal bewerkingen toevoegen/bijwerken/verwijderen die worden uitgevoerd op betrouwbare verzamelingen per seconde.|
| Flush Bytes/sec aanmelden | Het aantal bytes dat door de Transactionele Replicator per seconde naar de schijf wordt gespoeld |
| Throttled Operations/sec | Het aantal bewerkingen dat elke seconde door de Transactionele Replicator is afgewezen vanwege beperking. |
| Avg. Transactie ms/Commit | Gemiddelde commit latency per transactie in milliseconden |
| Avg. Flush Latency (ms) | Gemiddelde duur van schijfspoelbewerkingen die door de Transactionele Replicator in milliseconden zijn geïnitieerd |

### <a name="tstore-performance-counters"></a>TStore-prestatiemeteritems

De runtime van Reliable Services zendt `Service Fabric TStore` de volgende gebeurtenissen uit onder de categorie

 Naam van het meteritem | Beschrijving |
| --- | --- |
| Aantal objecten | Het aantal artikelen in de winkel.|
| Schijfgrootte | De totale schijfgrootte, in bytes, van controlepuntenbestanden voor de winkel.|
| Schrijfbytes voor controlepuntbestand per seconde | Het aantal bytes per seconde geschreven voor het meest recente controlepuntbestand.|
| Bytes voor schijfoverdracht per seconde kopiëren | Het aantal gelezen schijfbytes (op de primaire replica) of geschreven (op een secundaire replica) per seconde tijdens een winkelkopie.|

## <a name="next-steps"></a>Volgende stappen
[EventSource-providers in PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
