---
title: Azure Service Fabric-gebeurtenis analyse met Azure Monitor-logboeken
description: Meer informatie over het visualiseren en analyseren van gebeurtenissen met behulp van Azure Monitor logboeken voor het bewaken en diagnosticeren van Azure Service Fabric-clusters.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 40dd930aa21e3056d5ecc908359215d6874ed8ae
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464734"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Gebeurtenis analyse en visualisatie met Azure Monitor-logboeken
 Met Azure Monitor logboeken worden telemetrie verzameld en geanalyseerd op basis van toepassingen en services die worden gehost in de Cloud en worden er analyse hulpprogramma's geboden waarmee u hun Beschik baarheid en prestaties kunt maximaliseren. In dit artikel wordt beschreven hoe u query's uitvoert in Azure Monitor Logboeken om inzicht te krijgen in wat er in uw cluster gebeurt. De volgende veelgestelde vragen worden behandeld:

* Hoe kan ik problemen met status gebeurtenissen oplossen?
* Hoe kan ik weet u wanneer een knoop punt uitvalt?
* Hoe kan ik weet of de services van mijn toepassing zijn gestart of gestopt?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Overzicht van de Log Analytics-werk ruimte

>[!NOTE] 
>Hoewel diagnostische opslag standaard wordt ingeschakeld tijdens het maken van het cluster, moet u nog steeds de Log Analytics-werk ruimte instellen om te lezen uit de diagnostische opslag.

Azure Monitor logboeken verzamelt gegevens van beheerde bronnen, waaronder een Azure Storage-tabel of een agent, en houdt deze bij in een centrale opslag plaats. De gegevens kunnen vervolgens worden gebruikt voor analyse, waarschuwingen en visualisatie, of verder exporteren. Azure Monitor logboeken ondersteunt gebeurtenissen, prestatie gegevens of andere aangepaste gegevens. Bekijk [de stappen voor het configureren van de diagnostische uitbrei ding voor het verzamelen van gebeurtenissen](service-fabric-diagnostics-event-aggregation-wad.md) en [stappen voor het maken van een log Analytics werkruimte voor het lezen van de gebeurtenissen in de opslag](service-fabric-diagnostics-oms-setup.md) om er zeker van te zijn dat gegevens in azure monitor logboeken worden geplaatst.

Nadat de gegevens zijn ontvangen door Azure Monitor-logboeken, heeft Azure verschillende *bewakings oplossingen* die voorverpakte oplossingen of operationele Dash boards zijn voor het bewaken van binnenkomende gegevens, aangepast aan verschillende scenario's. Dit zijn onder andere een *service Fabric-analyse* oplossing en een oplossing voor *containers* , die de twee meest relevant zijn voor diagnostische gegevens en bewaking bij het gebruik van service Fabric clusters. In dit artikel wordt beschreven hoe u de Service Fabric-analyse oplossing gebruikt, die wordt gemaakt met de werk ruimte.

## <a name="access-the-service-fabric-analytics-solution"></a>Toegang tot de Service Fabric-analyse oplossing

Ga in [Azure Portal](https://portal.azure.com)naar de resource groep waarin u de service Fabric-analyse oplossing hebt gemaakt.

Selecteer de resource **ServiceFabric\<nameOfOMSWorkspace\>** .

In `Summary`ziet u tegels in de vorm van een grafiek voor elk van de ingeschakelde oplossingen, inclusief één voor Service Fabric. Klik op de **service Fabric** grafiek om door te gaan naar de service Fabric-analyse oplossing.

![Service Fabric oplossing](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

In de volgende afbeelding ziet u de start pagina van de Service Fabric-analyse oplossing. Deze start pagina bevat een moment opname van wat er in uw cluster gebeurt.

![Service Fabric oplossing](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Als u Diagnostische gegevens hebt ingeschakeld bij het maken van het cluster, kunt u gebeurtenissen weer geven voor 

* [Service Fabric cluster gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors van de programmeer model gebeurtenissen](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services van de programmeer model gebeurtenissen](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Naast de Service Fabric Events uit het vak, kunnen meer gedetailleerde systeem gebeurtenissen worden verzameld door [de configuratie van de diagnostische extensie](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)bij te werken.

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Service Fabric gebeurtenissen weer geven, met inbegrip van acties op knoop punten

Klik op de pagina Service Fabric-analyse op de grafiek voor **service Fabric gebeurtenissen**.

![Operationele kanaal van Service Fabric oplossing](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Klik op **lijst** om de gebeurtenissen in een lijst weer te geven. Hier worden alle systeem gebeurtenissen weer gegeven die zijn verzameld. Ter referentie zijn deze van de **WADServiceFabricSystemEventsTable** in het Azure Storage-account en ook de betrouw bare Services en actors-gebeurtenissen die u hierna ziet, zijn afkomstig uit die respectieve tabellen.
    
![Operationeel kanaal opvragen](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

U kunt ook op het vergroot glas aan de linkerkant klikken en de Kusto-query taal gebruiken om te vinden wat u zoekt. Als u bijvoorbeeld alle acties wilt vinden die worden uitgevoerd op knoop punten in het cluster, kunt u de volgende query gebruiken. De onderstaande gebeurtenis-Id's vindt u in de [Naslag informatie voor operationele kanaal gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

U kunt een query uitvoeren op veel meer velden, zoals de specifieke knoop punten (computer) van de systeem service (taak naam).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Service Fabric betrouw bare service-en actor gebeurtenissen weer geven

Klik op de pagina Service Fabric-analyse op de grafiek voor **reliable Services**.

![Service Fabric oplossing Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Klik op **lijst** om de gebeurtenissen in een lijst weer te geven. Hier kunt u gebeurtenissen van de betrouw bare Services bekijken. U kunt verschillende gebeurtenissen zien wanneer de service runasync wordt gestart en voltooid, wat doorgaans gebeurt bij implementaties en upgrades. 

![Query Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Betrouw bare actor gebeurtenissen kunnen op een vergelijk bare manier worden weer gegeven. Als u gedetailleerdere gebeurtenissen voor betrouw bare actoren wilt configureren, moet u de `scheduledTransferKeywordFilter` in de configuratie voor de diagnostische extensie wijzigen (zie hieronder). Details over de waarden hiervoor vindt u in de [Naslag informatie over betrouw bare evenementen van actors](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

De Kusto-query taal is krachtig. U kunt ook een andere waardevolle query uitvoeren om te bepalen welke knoop punten de meeste gebeurtenissen genereren. In de query in de onderstaande scherm afbeelding ziet u Service Fabric operationele gebeurtenissen geaggregeerd met de specifieke service en het opgegeven knoop punt.

![Query gebeurtenissen per knoop punt](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Volgende stappen

* Als u de bewaking van infra structuur wilt inschakelen, zoals prestatie meter items, moet u [de log Analytics agent toevoegen](service-fabric-diagnostics-oms-agent.md). De agent verzamelt prestatie meter items en voegt deze toe aan uw bestaande werk ruimte.
* Voor on-premises clusters biedt Azure Monitor-Logboeken een gateway (HTTP forward proxy) die kan worden gebruikt om gegevens te verzenden naar Azure Monitor-Logboeken. Meer informatie hierover vindt [u bij het verbinden van computers zonder Internet toegang tot Azure monitor-logboeken met behulp van de log Analytics gateway](../azure-monitor/platform/gateway.md).
* [Automatische waarschuwingen](../log-analytics/log-analytics-alerts.md) configureren voor ondersteuning bij detectie en diagnose.
* U kunt vertrouwd raken met de [Zoek-en query](../log-analytics/log-analytics-log-searches.md) functies die beschikbaar worden gesteld als onderdeel van Azure monitor Logboeken.
* Meer gedetailleerd overzicht van Azure Monitor-logboeken en wat het biedt, Lees [Wat is Azure monitor logboeken?](../operations-management-suite/operations-management-suite-overview.md).
