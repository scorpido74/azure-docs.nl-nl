---
title: Een cluster van servicefabric bewaken in Azure
description: In deze zelfstudie leert u hoe u een cluster controleren door servicefabric-gebeurtenissen te bekijken, de EventStore-API's op te vragen, perf-tellers te controleren en statusrapporten te bekijken.
author: srrengar
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: ab58d622511e0d5793eb6df312bc3fd6dd15bfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75376627"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Zelfstudie: Een cluster van servicefabric in Azure bewaken

Monitoring en diagnostiek zijn essentieel voor het ontwikkelen, testen en implementeren van workloads in elke cloudomgeving. Deze zelfstudie is deel twee van een reeks en laat u zien hoe u een cluster servicestructuur controleren en diagnosticeren met behulp van gebeurtenissen, prestatiemeteritems en gezondheidsrapporten.   Voor meer informatie, lees het overzicht over [cluster monitoring](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) en [infrastructuur monitoring](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Servicefabric-gebeurtenissen weergeven
> * Query EventStore API's voor clustergebeurtenissen
> * Monitor infrastructuur/verzamel perf-tellers
> * Clusterstatusrapporten weergeven

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

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installeer [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) of [Azure CLI](/cli/azure/install-azure-cli).
* Een veilig [Windows-cluster maken](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* Verzameling [diagnostische diagnoses](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) instellen voor het cluster
* De [EventStore-service](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) in het cluster inschakelen
* [Azure Monitor-logboeken en de agent Logboekanalyse](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) voor het cluster configureren

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Servicefabric-gebeurtenissen weergeven met Azure Monitor-logboeken

Azure Monitor-logboeken verzamelt en analyseert telemetrie van toepassingen en services die in de cloud worden gehost en biedt analysetools om u te helpen hun beschikbaarheid en prestaties te maximaliseren. U query's uitvoeren in Azure Monitor-logboeken om inzichten te verkrijgen en problemen op te lossen wat er in uw cluster gebeurt.

Als u toegang wilt krijgen tot de Oplossing Service Fabric Analytics, gaat u naar de [Azure-portal](https://portal.azure.com) en selecteert u de brongroep waarin u de Service Fabric Analytics-oplossing hebt gemaakt.

Selecteer de resource **ServiceFabric(mysfomsworkspace)**.

In **Overzicht** ziet u tegels in de vorm van een grafiek voor elk van de ingeschakelde oplossingen, waaronder een voor Service Fabric. Klik op de grafiek **Service Fabric** om door te gaan naar de Service Fabric Analytics-oplossing.

![Service Fabric-oplossing](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

De volgende afbeelding toont de startpagina van de Service Fabric Analytics-oplossing. Deze startpagina biedt een momentopnameweergave van wat er in uw cluster gebeurt.

![Service Fabric-oplossing](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Als u diagnostische gegevens hebt ingeschakeld bij het maken van het cluster, u gebeurtenissen 

* [Clustergebeurtenissen voor servicefabric](service-fabric-diagnostics-event-generation-operational.md)
* [Betrouwbare acteurs programmeren modelevenementen](service-fabric-reliable-actors-diagnostics.md)
* [Programmamodelgebeurtenissen voor betrouwbare services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Naast de Service Fabric-gebeurtenissen kunnen meer gedetailleerde systeemgebeurtenissen worden verzameld door [de config van uw diagnostische extensie bij](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)te werken.

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Servicefabric-gebeurtenissen weergeven, inclusief acties op knooppunten

Klik op de pagina Service Fabric Analytics op de grafiek voor **clustergebeurtenissen.**  De logboeken voor alle systeemgebeurtenissen die zijn verzameld, worden weergegeven. Ter referentie, deze zijn van de **WADServiceFabricSystemEventsTable** in het Azure Storage-account, en ook de betrouwbare services en actoren gebeurtenissen die u ziet volgende zijn van die respectieve tabellen.
    
![Operationeel kanaal query's](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

De query maakt gebruik van de Kusto-querytaal, die u wijzigen om te verfijnen wat u zoekt. Als u bijvoorbeeld alle acties op knooppunten in het cluster wilt vinden, u de volgende query gebruiken. De onderstaande gebeurtenis-id's zijn te vinden in de [referentie voor operationele kanaalgebeurtenissen](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

De Kusto-querytaal is krachtig. Hier zijn enkele andere nuttige query's.

Maak een *opzoektabel servicefabricEvent* als door de gebruiker gedefinieerde functie door de query als functie op te slaan met alias ServiceFabricEvent:

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

Operationele gebeurtenissen retourneren die in het laatste uur zijn geregistreerd:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Operationele gebeurtenissen retourneren met EventId == 18604 en EventName == 'NodeDownOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Operationele gebeurtenissen retourneren met EventId == 18604 en EventName == 'NodeUpOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Retourneert statusrapporten met HealthState == 3 (Fout) en extra eigenschappen uit het veld EventMessage:

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

Retourneer een tijdsschema met EventId != 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Ontvang operationele gebeurtenissen van Service Fabric samengevoegd met de specifieke service en het knooppunt:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Geef het aantal Service Fabric-gebeurtenissen weer op EventId / EventName met behulp van een cross-resource query:

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

### <a name="view-service-fabric-application-events"></a>Servicefabric-toepassingsgebeurtenissen weergeven

U gebeurtenissen bekijken voor de betrouwbare services en betrouwbare toepassingen die op het cluster zijn geïmplementeerd.  Klik op de pagina Service Fabric Analytics op de grafiek voor **toepassingsgebeurtenissen**.

Voer de volgende query uit om gebeurtenissen van uw betrouwbare servicestoepassingen weer te geven:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

U verschillende gebeurtenissen zien voor wanneer de runasync van de service is gestart en voltooid, wat meestal gebeurt bij implementaties en upgrades.

![Betrouwbare services voor servicefabric-oplossingen](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

U ook evenementen vinden voor de betrouwbare service met ServiceName == fabric:/Watchdog/WatchdogService:

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Betrouwbare acteur gebeurtenissen kunnen worden bekeken op een soortgelijke manier:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Als u meer gedetailleerde gebeurtenissen wilt configureren `scheduledTransferKeywordFilter` voor betrouwbare actoren, u de in de config wijzigen voor de diagnostische extensie in de clustersjabloon. Details over de waarden voor deze zijn in de [betrouwbare actoren gebeurtenissen referentie](service-fabric-reliable-actors-diagnostics.md#keywords).

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

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Prestatiemeteritems weergeven met Azure Monitor-logboeken
Als u prestatiemeteritems wilt weergeven, gaat u naar de [Azure-portal](https://portal.azure.com) en de resourcegroep waarin u de Service Fabric Analytics-oplossing hebt gemaakt. 

Selecteer de resource **ServiceFabric(mysfomsworkspace)** en vervolgens **Log Analytics Workspace**en vervolgens Geavanceerde **instellingen**.

Klik **op Gegevens**en klik vervolgens op **Prestatiemeteritems van Windows**. Er is een lijst met standaardtellers die u inschakelen en u het interval voor verzameling ook instellen. U ook [extra prestatiemeteritems](service-fabric-diagnostics-event-generation-perf.md) toevoegen om te verzamelen. Het juiste formaat wordt verwezen in dit [artikel](/windows/desktop/PerfCtrs/specifying-a-counter-path). Klik **op Opslaan**en klik vervolgens op **OK.**

Sluit het blad Geavanceerde instellingen en selecteer **het overzicht van werkruimte** onder de kop **Algemeen.** Voor elk van de ingeschakelde oplossingen is er een grafische tegel, waaronder een voor Service Fabric. Klik op de grafiek **Service Fabric** om door te gaan naar de Service Fabric Analytics-oplossing.

Er zijn grafische tegels voor operationele kanaal en betrouwbare diensten evenementen. De grafische weergave van de gegevens die binnenkomen voor de tellers die u hebt geselecteerd, wordt weergegeven onder **Knooppuntstatistieken**. 

Selecteer de grafiek **Containermetrische** gegevens om meer details te bekijken. U ook query's op prestatiemetergegevens op dezelfde manier als clustergebeurtenissen en filter op de knooppunten, perf teller naam, en waarden met behulp van de Kusto query taal.

## <a name="query-the-eventstore-service"></a>De EventStore-service opvragen
De [EventStore-service](service-fabric-diagnostics-eventstore.md) biedt een manier om de status van uw cluster of workloads op een bepaald moment te begrijpen. De EventStore is een stateful Service Fabric-service die gebeurtenissen uit het cluster bijhoudt. De gebeurtenissen worden weergegeven via de [Service Fabric Explorer,](service-fabric-visualizing-your-cluster.md)REST en API's. EventStore stelt het cluster rechtstreeks op om diagnostische gegevens op te halen over een entiteit in uw cluster Om een volledige lijst met gebeurtenissen te zien die beschikbaar zijn in de EventStore, raadpleegt [u Gebeurtenissen van servicefabric](service-fabric-diagnostics-event-generation-operational.md).

De EventStore API's kunnen programmatisch worden opgevraagd via de [clientbibliotheek Service Fabric.](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)

Hier vindt u een voorbeeldaanvraag voor alle clustergebeurtenissen tussen 2018-04-03T18:00:00Z en 2018-04T18:00:00Z, via de functie GetClusterEventListAsync.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Hier is nog een voorbeeld dat query's voor de clusterstatus en alle knooppuntgebeurtenissen in september 2018 en deze afdrukken.

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


## <a name="monitor-cluster-health"></a>Clusterstatus controleren
Service Fabric introduceert een [gezondheidsmodel](service-fabric-health-introduction.md) met gezondheidsinstanties waarop systeemcomponenten en waakhonden lokale omstandigheden kunnen melden die ze controleren. Het [gezondheidsarchief](service-fabric-health-introduction.md#health-store) verzamelt alle gezondheidsgegevens om te bepalen of entiteiten in orde zijn.

Het cluster wordt automatisch gevuld met statusrapporten die door de systeemcomponenten worden verzonden. Lees meer bij [Systeemstatusrapporten gebruiken om problemen op te lossen](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric stelt statusquery's bloot voor elk van de ondersteunde [entiteitstypen.](service-fabric-health-introduction.md#health-entities-and-hierarchy) Ze zijn toegankelijk via de API, met behulp van methoden op [FabricClient.HealthManager,](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet)PowerShell-cmdlets en REST. Deze query's geven volledige statusinformatie over de entiteit weer: de geaggregeerde status, statusgebeurtenissen van entiteiten, statussen van de gezondheid van kinderen (indien van toepassing), ongezonde evaluaties (wanneer de entiteit niet gezond is) en gezondheidsstatistieken voor kinderen (wanneer van toepassing).

### <a name="get-cluster-health"></a>Clusterstatus krijgen
De [cmdlet Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) retourneert de status van de clusterentiteit en bevat de status van toepassingen en knooppunten (kinderen van het cluster).  Maak eerst verbinding met het cluster met de [cmdlet Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

De status van het cluster is 11 knooppunten, de systeemtoepassing en de fabric:/Voting geconfigureerd zoals beschreven.

In het volgende voorbeeld wordt de clusterstatus weergegeven met standaardstatusbeleid. De 11 knooppunten zijn in orde, maar de samengevoegde status van het cluster is Fout omdat de toepassing fabric:/Voting in Fout staat. Houd er rekening mee hoe de ongezonde evaluaties details geven over de voorwaarden die de geaggregeerde status hebben geactiveerd.

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

In het volgende voorbeeld wordt de status van het cluster opgevolgd met behulp van een aangepast toepassingsbeleid. Het filtert resultaten om alleen toepassingen en knooppunten in fout of waarschuwing te krijgen. In dit voorbeeld worden geen knooppunten geretourneerd, omdat ze allemaal gezond zijn. Alleen de stof:/Stemtoepassing respecteert het applicatiefilter. Omdat het aangepaste beleid aangeeft waarschuwingen te beschouwen als fouten voor de fabric:/Voting-toepassing, wordt de toepassing als fout beoordeeld en het cluster dus.

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

### <a name="get-node-health"></a>Zorg voor knooppuntgezondheid
De [cmdlet Get-ServiceFabricNodeHealth retourneert](/powershell/module/servicefabric/get-servicefabricnodehealth) de status van een knooppuntentiteit en bevat de statusgebeurtenissen die op het knooppunt worden gerapporteerd. Maak eerst verbinding met het cluster met de [cmdlet Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) In het volgende voorbeeld wordt de status van een specifiek knooppunt weergegeven met standaardstatusbeleid:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

In het volgende voorbeeld wordt de status van alle knooppunten in het cluster:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>De status van systeemservice ophalen 

Krijg de geaggregeerde status van de systeemservices:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Servicefabric-gebeurtenissen weergeven
> * Query EventStore API's voor clustergebeurtenissen
> * Monitor infrastructuur/verzamel perf-tellers
> * Clusterstatusrapporten weergeven

Ga vervolgens naar de volgende zelfstudie om te leren hoe u een cluster schalen.
> [!div class="nextstepaction"]
> [Een cluster schalen](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
