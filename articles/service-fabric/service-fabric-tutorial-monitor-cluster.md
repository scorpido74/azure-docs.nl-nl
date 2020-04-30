---
title: Een Service Fabric cluster in azure bewaken
description: In deze zelf studie leert u hoe u een cluster kunt bewaken door Service Fabric gebeurtenissen te bekijken, query's uit te geven op de Event Store-Api's, prestatie meter items te controleren en status rapporten weer te geven.
author: srrengar
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: ab58d622511e0d5793eb6df312bc3fd6dd15bfd6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75376627"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Zelf studie: een Service Fabric cluster in azure bewaken

Bewaking en diagnose zijn essentieel voor het ontwikkelen, testen en implementeren van werk belastingen in elke cloud omgeving. Deze zelf studie is deel twee van een reeks en laat zien hoe u een Service Fabric cluster kunt controleren en diagnosticeren met gebeurtenissen, prestatie meter items en status rapporten.   Lees voor meer informatie het overzicht over [cluster bewaking](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) en [infrastructuur bewaking](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Service Fabric gebeurtenissen weer geven
> * Query's uitvoeren op Event Store-Api's voor cluster gebeurtenissen
> * Infra structuur controleren/prestatie meter items verzamelen
> * Status rapporten van het cluster weer geven

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * Een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) maken in Azure met behulp van een sjabloon
> * Een cluster bewaken
> * [Een cluster in- of uitschalen](service-fabric-tutorial-scale-cluster.md)
> * [De runtime van een cluster upgraden](service-fabric-tutorial-upgrade-cluster.md)
> * [Een cluster verwijderen](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan
* Installeer [Azure Power shell](https://docs.microsoft.com/powershell/azure/install-Az-ps) of [Azure cli](/cli/azure/install-azure-cli).
* Een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) maken 
* De [verzameling diagnostische gegevens](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) instellen voor het cluster
* De [Event Store-service](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) in het cluster inschakelen
* [Azure monitor-logboeken en de log Analytics agent](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) voor het cluster configureren

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Service Fabric gebeurtenissen weer geven met behulp van Azure Monitor-logboeken

Met Azure Monitor logboeken worden telemetrie verzameld en geanalyseerd op basis van toepassingen en services die worden gehost in de Cloud en worden er analyse hulpprogramma's geboden waarmee u hun Beschik baarheid en prestaties kunt maximaliseren. U kunt query's uitvoeren in Azure Monitor-Logboeken om inzicht te krijgen en problemen op te lossen wat er gebeurt in uw cluster.

Als u toegang wilt krijgen tot de Service Fabric-analyse oplossing, gaat u naar de [Azure Portal](https://portal.azure.com) en selecteert u de resource groep waarin u de service Fabric-analyse oplossing hebt gemaakt.

Selecteer de resource **-ServiceFabric (mysfomsworkspace)**.

In het **overzicht** ziet u tegels in de vorm van een grafiek voor elk van de ingeschakelde oplossingen, met inbegrip van een voor service Fabric. Klik op de **service Fabric** grafiek om door te gaan naar de service Fabric-analyse oplossing.

![Service Fabric oplossing](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

In de volgende afbeelding ziet u de start pagina van de Service Fabric-analyse oplossing. Deze start pagina bevat een moment opname van wat er in uw cluster gebeurt.

![Service Fabric oplossing](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Als u Diagnostische gegevens hebt ingeschakeld bij het maken van het cluster, kunt u gebeurtenissen weer geven voor 

* [Service Fabric cluster gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors van de programmeer model gebeurtenissen](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services van de programmeer model gebeurtenissen](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Naast de Service Fabric Events uit het vak, kunnen meer gedetailleerde systeem gebeurtenissen worden verzameld door [de configuratie van de diagnostische extensie](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)bij te werken.

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Service Fabric gebeurtenissen weer geven, met inbegrip van acties op knoop punten

Klik op de pagina Service Fabric-analyse op de grafiek voor **cluster gebeurtenissen**.  De logboeken voor alle systeem gebeurtenissen die zijn verzameld, worden weer gegeven. Ter referentie zijn deze van de **WADServiceFabricSystemEventsTable** in het Azure Storage-account en ook de betrouw bare Services en actors-gebeurtenissen die u hierna ziet, zijn afkomstig uit die respectieve tabellen.
    
![Operationeel kanaal opvragen](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

De query gebruikt de Kusto-query taal, die u kunt aanpassen om te verfijnen wat u zoekt. Als u bijvoorbeeld alle acties wilt vinden die worden uitgevoerd op knoop punten in het cluster, kunt u de volgende query gebruiken. De onderstaande gebeurtenis-Id's vindt u in de [Naslag informatie voor operationele kanaal gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

De Kusto-query taal is krachtig. Hier volgen enkele andere nuttige query's.

Maak een opzoek tabel *ServiceFabricEvent* als door de gebruiker gedefinieerde functie door de query op te slaan als een functie met alias ServiceFabricEvent:

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

Operationele gebeurtenissen retour neren die zijn vastgelegd in het afgelopen uur:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Operationele gebeurtenissen retour neren met gebeurtenis-Event = = 18604 en eventname = = ' NodeDownOperational ':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Operationele gebeurtenissen retour neren met gebeurtenis-Event = = 18604 en eventname = = ' NodeUpOperational ':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Retourneert status rapporten met HealthState = = 3 (fout) en extraheer extra eigenschappen van het veld EventMessage:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

Een tijd diagram met gebeurtenissen retour neren met de gebeurtenis-waarde! = 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Service Fabric operationele gebeurtenissen ophalen die worden samengevoegd met de specifieke service en het knoop punt:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Genereer het aantal Service Fabric gebeurtenissen op gebeurtenis-voor-en-gebeurtenis namen met behulp van een query voor meerdere bronnen:

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Service Fabric toepassings gebeurtenissen weer geven

U kunt gebeurtenissen weer geven voor de betrouw bare Services en de reliable actor-toepassingen die op het cluster zijn geïmplementeerd.  Klik op de pagina Service Fabric-analyse op de grafiek voor **toepassings gebeurtenissen**.

Voer de volgende query uit om gebeurtenissen van uw reliable Services-toepassingen weer te geven:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

U kunt verschillende gebeurtenissen zien wanneer de service runasync wordt gestart en voltooid, wat doorgaans gebeurt bij implementaties en upgrades.

![Service Fabric oplossing Reliable Services](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

U kunt ook gebeurtenissen vinden voor de betrouw bare service met ServiceName = = "Fabric:/watchdog/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Betrouw bare actor gebeurtenissen kunnen op een vergelijk bare manier worden weer gegeven:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Als u meer gedetailleerde gebeurtenissen voor betrouw bare actors wilt configureren `scheduledTransferKeywordFilter` , kunt u de in de configuratie voor de diagnostische extensie in de cluster sjabloon wijzigen. Details over de waarden hiervoor vindt u in de [Naslag informatie over betrouw bare evenementen van actors](service-fabric-reliable-actors-diagnostics.md#keywords).

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

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Prestatie meter items weer geven met Azure Monitor-logboeken
Als u prestatie meter items wilt weer geven, gaat u naar de [Azure Portal](https://portal.azure.com) en de resource groep waarin u de service Fabric-analyse oplossing hebt gemaakt. 

Selecteer de resource **-ServiceFabric (mysfomsworkspace)**, vervolgens **log Analytics werk ruimte**en **Geavanceerde instellingen**.

Klik op **gegevens**en vervolgens op **Windows-prestatie meter items**. Er is een lijst met standaard tellers die u kunt inschakelen en u kunt het interval voor de verzameling ook instellen. U kunt ook [aanvullende prestatie meter items](service-fabric-diagnostics-event-generation-perf.md) toevoegen om te verzamelen. In dit [artikel](/windows/desktop/PerfCtrs/specifying-a-counter-path)wordt verwezen naar de juiste indeling. Klik op **Opslaan**en vervolgens op **OK**.

Sluit de Blade geavanceerde instellingen en selecteer **werk ruimte overzicht** onder de kop **Algemeen** . Voor elk van de oplossingen die zijn ingeschakeld, is er een grafische tegel, met inbegrip van een voor Service Fabric. Klik op de **service Fabric** grafiek om door te gaan naar de service Fabric-analyse oplossing.

Er zijn grafische tegels voor operationele kanaal-en reliable Services-gebeurtenissen. De grafische weer gave van de gegevens die in voor de items die u hebt geselecteerd, wordt onder **metrische gegevens van knoop punten**weer gegeven. 

Selecteer de **metrische** grafiek van de container om meer details te bekijken. U kunt ook een query uitvoeren op gegevens van prestatie meter items op dezelfde manier als cluster gebeurtenissen en filteren op de knoop punten, de naam van het prestatie meter item en waarden met behulp van de Kusto-query taal.

## <a name="query-the-eventstore-service"></a>Query's uitvoeren op de Event Store-service
De [Event Store-service](service-fabric-diagnostics-eventstore.md) biedt een manier om inzicht te krijgen in de status van uw cluster of workloads op een bepaald moment. De Event Store is een stateful Service Fabric service die gebeurtenissen van het cluster onderhoudt. De gebeurtenissen worden weer gegeven via de [service Fabric Explorer](service-fabric-visualizing-your-cluster.md), rest en api's. Event Store vraagt het cluster direct om diagnostische gegevens op te halen uit een wille keurige entiteit in uw cluster om een volledige lijst van gebeurtenissen te zien die beschikbaar zijn in de Event Store, Zie [service Fabric-gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md).

De Event Store-Api's kunnen programmatisch worden opgevraagd met behulp van de [service Fabric-client bibliotheek](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Hier volgt een voor beeld van een aanvraag voor alle cluster gebeurtenissen tussen 2018-04-03T18:00:00Z en 2018-04-04T18:00:00Z, via de functie GetClusterEventListAsync.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Hier volgt nog een voor beeld van query's voor de cluster status en alle knooppunt gebeurtenissen in september 2018 en worden afgedrukt.

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>Cluster status controleren
Service Fabric introduceert een [status model](service-fabric-health-introduction.md) met status entiteiten waarop systeem onderdelen en watchdog lokale voor waarden kunnen rapporteren die ze bewaken. Met de [Health Store](service-fabric-health-introduction.md#health-store) worden alle status gegevens geaggregeerd om te bepalen of de entiteiten in orde zijn.

Het cluster wordt automatisch ingevuld met status rapporten die worden verzonden door de systeem onderdelen. Meer informatie [over het gebruik van systeem status rapporten om problemen op te lossen](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric geeft status query's weer voor elk [type ondersteunde entiteit](service-fabric-health-introduction.md#health-entities-and-hierarchy). Ze kunnen worden geopend via de API, met behulp van methoden op [FabricClient. HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), Power shell-cmdlets en rest. Deze query's retour neren gegevens over de status van de entiteit: de geaggregeerde status, de status van de entiteit, de status van de onderliggende status (indien van toepassing), de niet-Health-evaluaties (wanneer de entiteit niet in orde is) en de statistieken van de onderliggende status (indien van toepassing).

### <a name="get-cluster-health"></a>Cluster status ophalen
De [cmdlet Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) retourneert de status van de cluster entiteit en bevat de statussen van toepassingen en knoop punten (onderliggende items van het cluster).  Maak eerst verbinding met het cluster met behulp van de [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).

De status van het cluster is 11 knoop punten, de systeem toepassing en Fabric:/stem geconfigureerd zoals beschreven.

In het volgende voor beeld wordt de status van het cluster opgehaald met een standaard status beleid. De 11 knoop punten zijn in orde, maar de samengevoegde status van het cluster is fout, omdat de toepassing Fabric:/stem een fout heeft. U ziet hoe de onjuiste evaluaties details bevatten over de omstandigheden die de geaggregeerde status hebben geactiveerd.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

In het volgende voor beeld wordt de status van het cluster met behulp van een aangepast toepassings beleid opgehaald. De resultaten worden gefilterd om alleen toepassingen en knoop punten met een fout of waarschuwing te verkrijgen. In dit voor beeld worden er geen knoop punten geretourneerd, omdat ze allemaal in orde zijn. Alleen de fabric:/stem toepassing respecteert het toepassingen filter. Omdat het aangepaste beleid specificeert om waarschuwingen te beschouwen als fouten voor de toepassing Fabric:/stem, wordt de toepassing geëvalueerd als fout en is het cluster.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>Status van knoop punt ophalen
De [cmdlet Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) retourneert de status van een knooppunt entiteit en bevat de status gebeurtenissen die op het knoop punt zijn gerapporteerd. Maak eerst verbinding met het cluster met behulp van de [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps). In het volgende voor beeld wordt de status van een specifiek knoop punt met behulp van standaard status beleid opgehaald:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

In het volgende voor beeld wordt de status van alle knoop punten in het cluster opgehaald:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Status van systeem service ophalen 

De cumulatieve status van de systeem services ophalen:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Service Fabric gebeurtenissen weer geven
> * Query's uitvoeren op Event Store-Api's voor cluster gebeurtenissen
> * Infra structuur controleren/prestatie meter items verzamelen
> * Status rapporten van het cluster weer geven

Ga vervolgens verder met de volgende zelf studie voor meer informatie over het schalen van een cluster.
> [!div class="nextstepaction"]
> [Een cluster schalen](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
