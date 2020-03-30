---
title: Azure Service Fabric-gebeurtenisanalyse met Azure Monitor-logboeken
description: Meer informatie over het visualiseren en analyseren van gebeurtenissen met Azure Monitor-logboeken voor monitoring en diagnostiek van Azure Service Fabric-clusters.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 40dd930aa21e3056d5ecc908359215d6874ed8ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464734"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Gebeurtenisanalyse en visualisatie met Azure Monitor-logboeken
 Azure Monitor-logboeken verzamelt en analyseert telemetrie van toepassingen en services die in de cloud worden gehost en biedt analysetools om u te helpen hun beschikbaarheid en prestaties te maximaliseren. In dit artikel wordt beschreven hoe u query's uitvoert in Azure Monitor-logboeken om inzichten te verkrijgen en problemen op te lossen wat er in uw cluster gebeurt. De volgende veelgestelde vragen komen aan bod:

* Hoe los ik gezondheidsgebeurtenissen op?
* Hoe weet ik wanneer een knooppunt naar beneden gaat?
* Hoe weet ik of de services van mijn toepassing zijn gestart of gestopt?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Overzicht van de werkruimte Log Analytics

>[!NOTE] 
>Hoewel diagnostische opslag standaard is ingeschakeld op de tijd voor het maken van het cluster, moet u de werkruimte Log Analytics nog steeds instellen om te lezen vanuit de diagnostische opslag.

Azure Monitor-logboeken verzamelen gegevens van beheerde resources, waaronder een Azure-opslagtabel of een agent, en onderhouden deze in een centrale opslagplaats. De gegevens kunnen vervolgens worden gebruikt voor analyse, waarschuwing en visualisatie, of verder exporteren. Azure Monitor-logboeken ondersteunen gebeurtenissen, prestatiegegevens of andere aangepaste gegevens. Bekijk [de stappen om de diagnostische extensie te configureren om gebeurtenissen](service-fabric-diagnostics-event-aggregation-wad.md) en stappen te verzamelen om een Log [Analytics-werkruimte te maken die u wilt lezen uit de gebeurtenissen in de opslag](service-fabric-diagnostics-oms-setup.md) om te controleren of gegevens naar Azure Monitor-logboeken worden verzonden.

Nadat gegevens zijn ontvangen door Azure Monitor-logboeken, beschikt Azure over verschillende *bewakingsoplossingen* die voorverpakte oplossingen of operationele dashboards zijn om binnenkomende gegevens te controleren, aangepast aan verschillende scenario's. Deze omvatten een *Service Fabric Analytics-oplossing* en een *Containers-oplossing,* die de twee meest relevante zijn voor diagnostiek en monitoring bij het gebruik van Service Fabric-clusters. In dit artikel wordt beschreven hoe u de Service Fabric Analytics-oplossing gebruikt, die is gemaakt met de werkruimte.

## <a name="access-the-service-fabric-analytics-solution"></a>Toegang tot de Service Fabric Analytics-oplossing

Ga in de [Azure Portal](https://portal.azure.com)naar de resourcegroep waarin u de Service Fabric Analytics-oplossing hebt gemaakt.

Selecteer de **naam\<ServiceFabric van\>resourceFabricOfOMSWorkspace**.

In `Summary`, ziet u tegels in de vorm van een grafiek voor elk van de ingeschakelde oplossingen, waaronder een voor Service Fabric. Klik op de grafiek **Service Fabric** om door te gaan naar de Service Fabric Analytics-oplossing.

![Service Fabric-oplossing](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

De volgende afbeelding toont de startpagina van de Service Fabric Analytics-oplossing. Deze startpagina biedt een momentopnameweergave van wat er in uw cluster gebeurt.

![Service Fabric-oplossing](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Als u diagnostische gegevens hebt ingeschakeld bij het maken van het cluster, u gebeurtenissen 

* [Clustergebeurtenissen voor servicefabric](service-fabric-diagnostics-event-generation-operational.md)
* [Betrouwbare acteurs programmeren modelevenementen](service-fabric-reliable-actors-diagnostics.md)
* [Programmamodelgebeurtenissen voor betrouwbare services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Naast de Service Fabric-gebeurtenissen kunnen meer gedetailleerde systeemgebeurtenissen worden verzameld door [de config van uw diagnostische extensie bij](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)te werken.

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Servicefabric-gebeurtenissen weergeven, inclusief acties op knooppunten

Klik op de pagina Service Fabric Analytics op de grafiek voor **Service Fabric-gebeurtenissen.**

![Operationeel kanaal voor servicefabric-oplossing](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Klik **op Lijst** om de gebeurtenissen in een lijst weer te geven. Eenmaal hier ziet u alle systeemgebeurtenissen die zijn verzameld. Ter referentie, deze zijn van de **WADServiceFabricSystemEventsTable** in het Azure Storage-account, en ook de betrouwbare services en actoren gebeurtenissen die u ziet volgende zijn van die respectieve tabellen.
    
![Operationeel kanaal query's](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

U ook op het vergrootglas aan de linkerkant klikken en de Kusto-querytaal gebruiken om te vinden wat u zoekt. Als u bijvoorbeeld alle acties op knooppunten in het cluster wilt vinden, u de volgende query gebruiken. De onderstaande gebeurtenis-id's zijn te vinden in de [referentie voor operationele kanaalgebeurtenissen](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

U op veel meer velden, zoals de specifieke knooppunten (Computer) de systeemservice (TaskName) query's uitvoeren.

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Service Fabric Reliable Service en Actor-gebeurtenissen bekijken

Klik op de pagina Service Fabric Analytics op de grafiek voor **betrouwbare services.**

![Betrouwbare services voor servicefabric-oplossingen](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Klik **op Lijst** om de gebeurtenissen in een lijst weer te geven. Hier u de gebeurtenissen van de betrouwbare diensten zien. U verschillende gebeurtenissen zien voor wanneer de runasync van de service is gestart en voltooid, wat meestal gebeurt bij implementaties en upgrades. 

![Betrouwbare services opvragen](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Betrouwbare acteur gebeurtenissen kunnen worden bekeken op een soortgelijke manier. Als u meer gedetailleerde gebeurtenissen wilt configureren `scheduledTransferKeywordFilter` voor betrouwbare actoren, moet u de in de config wijzigen voor de diagnostische extensie (zie hieronder). Details over de waarden voor deze zijn in de [betrouwbare actoren gebeurtenissen referentie](service-fabric-reliable-actors-diagnostics.md#keywords).

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

De Kusto-querytaal is krachtig. Een andere waardevolle query die u uitvoeren, is om erachter te komen welke knooppunten de meeste gebeurtenissen genereren. De query in de onderstaande schermafbeelding toont operationele gebeurtenissen van Service Fabric die zijn samengevoegd met de specifieke service en het knooppunt.

![Querygebeurtenissen per knooppunt](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Volgende stappen

* Als u infrastructuurbewaking wilt inschakelen, d.w.z. prestatiemeteritems, gaat u naar [het toevoegen van de log-analyse-agent](service-fabric-diagnostics-oms-agent.md). De agent verzamelt prestatiemeteritems en voegt deze toe aan uw bestaande werkruimte.
* Voor on-premises clusters bieden Azure Monitor-logboeken een gateway (HTTP Forward Proxy) die kan worden gebruikt om gegevens naar Azure Monitor-logboeken te verzenden. Lees daar meer over in [Het verbinden van computers zonder internettoegang tot Azure Monitor-logboeken met behulp van de Logboekanalysegateway.](../azure-monitor/platform/gateway.md)
* Configureer [geautomatiseerde waarschuwingen](../log-analytics/log-analytics-alerts.md) om te helpen bij detectie en diagnostiek.
* Maak kennis met de [functies voor het zoeken en opvragen van logboeken](../log-analytics/log-analytics-log-searches.md) die worden aangeboden als onderdeel van Azure Monitor-logboeken.
* Lees [Wat zijn Azure Monitor-logboeken?](../operations-management-suite/operations-management-suite-overview.md)
