---
title: De geaggregeerde status van Azure Service Fabric-entiteiten weergeven
description: Beschrijft hoe u de geaggregeerde status van Azure Service Fabric-entiteiten opvragen, weergeven en evalueren via statusquery's en algemene query's.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d02d8f717801bf51e43c9dafa5eb9379d0737674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464130"
---
# <a name="view-service-fabric-health-reports"></a>Gezondheidsrapporten van Service Fabric weergeven
Azure Service Fabric introduceert een [gezondheidsmodel](service-fabric-health-introduction.md) met statusentiteiten waarop systeemcomponenten en waakhonden lokale omstandigheden kunnen melden die ze controleren. Het [gezondheidsarchief](service-fabric-health-introduction.md#health-store) verzamelt alle gezondheidsgegevens om te bepalen of entiteiten in orde zijn.

Het cluster wordt automatisch gevuld met statusrapporten die door de systeemcomponenten worden verzonden. Lees meer bij [Systeemstatusrapporten gebruiken om problemen op te lossen](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric biedt meerdere manieren om de geaggregeerde status van de entiteiten te krijgen:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) of andere visualisatietools
* Statusquery's (via PowerShell, API of REST)
* Algemene query's die een lijst met entiteiten retourneren die de status hebben als een van de eigenschappen (via PowerShell, API of REST)

Om deze opties aan te tonen, gebruiken we een lokaal cluster met vijf knooppunten en de [toepassing fabric:/WordCount.](https://github.com/Azure-Samples/service-fabric-wordcount/raw/master/WordCountV1.sfpkg) De **fabric:/WordCount-toepassing** bevat twee standaardservices, `WordCountServiceType`een stateful service van `WordCountWebServiceType`het type en een stateless service van het type . Ik veranderde `ApplicationManifest.xml` de zeven doel replica's nodig voor de stateful service en een partitie. Omdat er slechts vijf knooppunten in het cluster zijn, rapporteren de systeemcomponenten een waarschuwing op de servicepartitie omdat deze onder het aantal objecten ligt.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Health in Service Fabric Explorer
Service Fabric Explorer biedt een visuele weergave van het cluster. In de afbeelding hieronder zie je dat:

* De **toepassingsstructuur:/WordCount** is rood (ten onrechte) omdat er een foutgebeurtenis is gerapporteerd door **MyWatchdog** voor de **beschikbaarheid van**de accommodatie.
* Een van de services, **fabric:/WordCount/WordCountService** is geel (in waarschuwing). De service is geconfigureerd met zeven replica's en het cluster heeft vijf knooppunten, zodat twee replica's niet kunnen worden geplaatst. Hoewel het hier niet wordt weergegeven, is de servicepartitie `System.FM` geel `Partition is below target replica or instance count`vanwege een systeemrapport dat zegt dat . De gele partitie activeert de gele service.
* Het cluster is rood vanwege de rode toepassing.

De evaluatie maakt gebruik van standaardbeleidsregels uit het clustermanifest en het toepassingsmanifest. Het zijn strikte beleidsmaatregelen en tolereren geen mislukkingen.

Weergave van het cluster met Service Fabric Explorer:

![Weergave van het cluster met Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Lees meer over [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Statusquery's
Service Fabric stelt statusquery's bloot voor elk van de ondersteunde [entiteitstypen.](service-fabric-health-introduction.md#health-entities-and-hierarchy) Ze zijn toegankelijk via de API, met behulp van methoden op [FabricClient.HealthManager,](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet)PowerShell-cmdlets en REST. Deze query's geven volledige statusinformatie over de entiteit weer: de geaggregeerde status, statusgebeurtenissen van entiteiten, statussen van de gezondheid van kinderen (indien van toepassing), ongezonde evaluaties (wanneer de entiteit niet gezond is) en gezondheidsstatistieken voor kinderen (wanneer van toepassing).

> [!NOTE]
> Een statusentiteit wordt geretourneerd wanneer deze volledig is gevuld in het statusarchief. De entiteit moet actief zijn (niet verwijderd) en een systeemrapport hebben. De bovenliggende entiteiten in de hiërarchieketen moeten ook systeemrapporten hebben. Als niet aan een van deze voorwaarden is voldaan, retourneren de statusquery's een [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) met [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` die aangeeft waarom de entiteit niet wordt geretourneerd.
>
>

De statusquery's moeten worden doorgegeven in de entiteits-id, die afhankelijk is van het entiteitstype. De query's accepteren optionele parameters voor het gezondheidsbeleid. Als er geen statusbeleid is opgegeven, worden de [statusbeleidsregels](service-fabric-health-introduction.md#health-policies) uit het cluster- of toepassingsmanifest gebruikt voor evaluatie. Als de manifesten geen definitie voor gezondheidsbeleid bevatten, worden de standaardstatusbeleidsregels gebruikt voor evaluatie. Het standaardstatusbeleid tolereert geen fouten. De query's accepteren ook filters voor het retourneren van slechts gedeeltelijke kinderen of gebeurtenissen - de query's die de opgegeven filters respecteren. Een ander filter maakt het mogelijk om de kinderstatistieken uit te sluiten.

> [!NOTE]
> De uitvoerfilters worden toegepast aan de serverzijde, zodat de antwoordgrootte van het bericht wordt verkleind. We raden u aan de uitvoerfilters te gebruiken om de geretourneerde gegevens te beperken, in plaats van filters toe te passen aan de clientzijde.
>
>

De status van een entiteit bevat:

* De geaggregeerde status van de entiteit. Berekend door het statusarchief op basis van entiteitsstatusrapporten, statussen van onderliggende statussen (indien van toepassing) en gezondheidsbeleid. Lees meer over [de evaluatie van de status van entiteit](service-fabric-health-introduction.md#health-evaluation).  
* De statusgebeurtenissen op de entiteit.
* De verzameling van gezondheidstoestanden van alle kinderen voor de entiteiten die kinderen kunnen krijgen. De statussen bevatten entiteits-id's en de geaggregeerde status. Als u de volledige status van een kind wilt krijgen, roept u de querystatus voor het type onderliggende entiteit aan en geeft u de onderliggende id door.
* De ongezonde evaluaties die wijzen op het rapport dat de status van de entiteit heeft geactiveerd, als de entiteit niet in orde is. De evaluaties zijn recursieve, met de kinderen gezondheid evaluaties die de huidige gezondheidstoestand geactiveerd. Een waakhond heeft bijvoorbeeld een fout gemeld tegen een replica. De toepassingsstatus toont een ongezonde evaluatie als gevolg van een ongezonde service; de service is ongezond als gevolg van een foutiaatpartitie; de partitie is ongezond als gevolg van een foutiaat. de replica is ongezond als gevolg van de waakhond fout gezondheidsrapport.
* De gezondheidsstatistieken voor alle kindertypen van de entiteiten die kinderen hebben. De clusterstatus toont bijvoorbeeld het totale aantal toepassingen, services, partities, replica's en geïmplementeerde entiteiten in het cluster. De servicestatus toont het totale aantal partities en replica's onder de opgegeven service.

## <a name="get-cluster-health"></a>Clusterstatus krijgen
Geeft als resultaat de status van de clusterentiteit en bevat de statussen van toepassingen en knooppunten (kinderen van het cluster). Invoer:

* [Optioneel] Het clusterstatusbeleid dat wordt gebruikt om de knooppunten en de clustergebeurtenissen te evalueren.
* [Optioneel] De beleidskaart voor toepassingsstatus, met het gezondheidsbeleid dat wordt gebruikt om het toepassingsmanifestbeleid te overschrijven.
* [Optioneel] Filters voor gebeurtenissen, knooppunten en toepassingen die aangeven welke items van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen, knooppunten en toepassingen worden gebruikt om de geaggregeerde status van de entiteit te evalueren, ongeacht het filter.
* [Optioneel] Filter om gezondheidsstatistieken uit te sluiten.
* [Optioneel] Filter om stof op te nemen:/Systeemgezondheidsstatistieken in de gezondheidsstatistieken. Alleen van toepassing wanneer de gezondheidsstatistieken niet worden uitgesloten. Standaard bevatten de statusstatistieken alleen statistieken voor gebruikerstoepassingen en niet de systeemtoepassing.

### <a name="api"></a>API
Als u de clusterstatus wilt opvragen, maakt u een `FabricClient` en roept u de [GetClusterHealthAsync-methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) aan op de **HealthManager**.

De volgende oproep krijgt de clusterstatus:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

De volgende code krijgt de clusterstatus met behulp van een aangepast clusterstatusbeleid en filters voor knooppunten en toepassingen. Het geeft aan dat de gezondheidsstatistieken de stof bevatten:/Systeemstatistieken. Hiermee wordt [ClusterHealthQueryDescription ,](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription)die de invoerinformatie bevat, maakt.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
De cmdlet om de clusterstatus te krijgen is [Get-ServiceFabricClusterHealth.](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth) Maak eerst verbinding met het cluster met de [cmdlet Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

De status van het cluster is vijf knooppunten, de systeemtoepassing en fabric:/WordCount geconfigureerd zoals beschreven.

De volgende cmdlet krijgt clusterstatus met behulp van standaardstatusbeleid. De geaggregeerde status is waarschuwing, omdat de toepassing fabric:/WordCount wordt gewaarschuwd. Houd er rekening mee hoe de ongezonde evaluaties details geven over de voorwaarden die de geaggregeerde status hebben geactiveerd.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.


NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

Met de volgende PowerShell-cmdlet krijgt u de status van het cluster met behulp van een aangepast toepassingsbeleid. Het filtert resultaten om alleen toepassingen en knooppunten in fout of waarschuwing te krijgen. Als gevolg hiervan worden er geen knooppunten geretourneerd, omdat ze allemaal gezond zijn. Alleen de fabric:/WordCount-toepassing respecteert het filter voor toepassingen. Omdat het aangepaste beleid aangeeft waarschuwingen te beschouwen als fouten voor de toepassing fabric:/WordCount, wordt de toepassing als fout beoordeeld en het cluster ook.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None
```

### <a name="rest"></a>REST
U clusterstatus krijgen met een [GET-aanvraag](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) of een [POST-verzoek](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) met gezondheidsbeleid dat in het lichaam is beschreven.

## <a name="get-node-health"></a>Zorg voor knooppuntgezondheid
Geeft als resultaat de status van een knooppuntentiteit en bevat de statusgebeurtenissen die op het knooppunt worden gerapporteerd. Invoer:

* [Vereist] De naam van het knooppunt dat het knooppunt identificeert.
* [Optioneel] De beleidsinstellingen voor clusterstatus die worden gebruikt om de status te evalueren.
* [Optioneel] Filters voor gebeurtenissen die aangeven welke items van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen worden gebruikt om de geaggregeerde status van de entiteit te evalueren, ongeacht het filter.

### <a name="api"></a>API
Als u de status van knooppunt `FabricClient` via de API wilt krijgen, maakt u een en roept u de [GetNodeHealthAsync-methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) aan op de HealthManager.

De volgende code krijgt de knooppuntstatus voor de opgegeven knooppuntnaam:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

De volgende code krijgt de knooppuntstatus voor de opgegeven knooppuntnaam en geeft door in het gebeurtenisfilter en het aangepaste beleid via [NodeHealthQueryDescription:](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription)

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
De cmdlet om de node health te krijgen is [Get-ServiceFabricNodeHealth.](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth) Maak eerst verbinding met het cluster met de [cmdlet Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)
Met de volgende cmdlet wordt de status knooppunt weergegeven met standaardstatusbeleid:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Met de volgende cmdlet wordt de status van alle knooppunten in het cluster:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
U knooppuntgezondheid krijgen met een [GET-verzoek](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) of een [post-verzoek](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) met gezondheidsbeleid dat in het lichaam is beschreven.

## <a name="get-application-health"></a>Toepassingsstatus aanvragen
Geeft als resultaat de status van een toepassingsentiteit. Het bevat de gezondheidstoestanden van de geïmplementeerde toepassing en servicekinderen. Invoer:

* [Vereist] De toepassingsnaam (URI) die de toepassing identificeert.
* [Optioneel] Het toepassingsstatusbeleid dat wordt gebruikt om het toepassingsmanifestbeleid te overschrijven.
* [Optioneel] Filters voor gebeurtenissen, services en geïmplementeerde toepassingen die aangeven welke vermeldingen van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen, services en geïmplementeerde toepassingen worden gebruikt om de geaggregeerde status van de entiteit te evalueren, ongeacht het filter.
* [Optioneel] Filter om de gezondheidsstatistieken uit te sluiten. Als dit niet is opgegeven, bevatten de statusstatistieken het aantal ok-, waarschuwing- en foutgegevens voor alle toepassingskinderen: services, partities, replica's, geïmplementeerde toepassingen en geïmplementeerde servicepakketten.

### <a name="api"></a>API
Als u de toepassingsstatus wilt opvragen, maakt u een `FabricClient` en belt u de [GetApplicationHealthAsync-methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) op zijn HealthManager.

De volgende code krijgt de toepassingsstatus voor de opgegeven toepassingsnaam (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

De volgende code krijgt de toepassingsstatus voor de opgegeven toepassingsnaam (URI), met filters en aangepast beleid opgegeven via [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
De cmdlet om de applicatiestatus te krijgen is [Get-ServiceFabricApplicationHealth.](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) Maak eerst verbinding met het cluster met de [cmdlet Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Met de volgende cmdlet wordt de status van de **stof geretourneerd:/WordCount-toepassing:**

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

De volgende PowerShell-cmdlet wordt ingevoerd in aangepast beleid. Het filtert ook kinderen en evenementen.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
U de status van toepassingen krijgen met een [GET-aanvraag](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) of een [POST-verzoek](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) met gezondheidsbeleid dat in het lichaam is beschreven.

## <a name="get-service-health"></a>Servicestatus krijgen
Geeft als resultaat de status van een serviceentiteit. Het bevat de partitiestatussen. Invoer:

* [Vereist] De servicenaam (URI) die de service identificeert.
* [Optioneel] Het toepassingsstatusbeleid dat wordt gebruikt om het toepassingsmanifestbeleid te overschrijven.
* [Optioneel] Filters voor gebeurtenissen en partities die aangeven welke items van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen en partities worden gebruikt om de geaggregeerde status van de entiteit te evalueren, ongeacht het filter.
* [Optioneel] Filter om gezondheidsstatistieken uit te sluiten. Als dit niet is opgegeven, worden in de statusstatistieken het aantal ok-, waarschuwing- en foutwaarden weergegeven voor alle partities en replica's van de service.

### <a name="api"></a>API
Als u de servicestatus via `FabricClient` de API wilt krijgen, maakt u een getServiceHealthAsync-methode en roept u de [GetServiceHealthAsync-methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) aan op zijn HealthManager.

In het volgende voorbeeld wordt de status van een service met opgegeven servicenaam (URI) opnaam gegeven:

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

De volgende code krijgt de servicestatus voor de opgegeven servicenaam (URI), waarbij filters en aangepast beleid worden opgegeven via [ServiceHealthQueryDescription:](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription)

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
De cmdlet om de servicestatus te krijgen is [Get-ServiceFabricServiceHealth.](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth) Maak eerst verbinding met het cluster met de [cmdlet Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Met de volgende cmdlet wordt de servicestatus weergegeven met standaardstatusbeleid:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning

HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
U servicestatus krijgen met een [GET-aanvraag](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) of een [POST-verzoek](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) met gezondheidsbeleid dat in het lichaam is beschreven.

## <a name="get-partition-health"></a>Partitiestatus krijgen
Geeft als resultaat de status van een partitieentiteit. Het bevat de replica statussen. Invoer:

* [Vereist] De partitie-ID (GUID) die de partitie identificeert.
* [Optioneel] Het toepassingsstatusbeleid dat wordt gebruikt om het toepassingsmanifestbeleid te overschrijven.
* [Optioneel] Filters voor gebeurtenissen en replica's die aangeven welke items van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen en replica's worden gebruikt om de geaggregeerde status van de entiteit te evalueren, ongeacht het filter.
* [Optioneel] Filter om gezondheidsstatistieken uit te sluiten. Als dit niet is opgegeven, geven de statusstatistieken aan hoeveel replica's in ok-, waarschuwings- en foutstatussen staan.

### <a name="api"></a>API
Als u de partitiestatus via `FabricClient` de API wilt krijgen, maakt u een en roept u de [GetPartitionHealthAsync-methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) aan op de HealthManager. Als u optionele parameters wilt opgeven, maakt u [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
De cmdlet om de partitiestatus te krijgen is [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Maak eerst verbinding met het cluster met de [cmdlet Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Met de volgende cmdlet wordt de status voor alle partities van de **fabric:/WordCount/WordCountService-service** en worden replicastatussen uitgefilterd:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)

                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A

                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None

                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
U partitiestatus krijgen met een [GET-aanvraag](/rest/api/servicefabric/sfclient-api-getpartitionhealth) of een [POST-verzoek](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) met gezondheidsbeleid dat in het lichaam is beschreven.

## <a name="get-replica-health"></a>Replicastatus krijgen
Geeft als resultaat de status van een statusvolle servicereplica of een serviceinstantie zonder staat. Invoer:

* [Vereist] De partitie-id (GUID) en replica-id die de replica identificeert.
* [Optioneel] De beleidsparameters voor toepassingsstatus die worden gebruikt om het toepassingsmanifestbeleid te overschrijven.
* [Optioneel] Filters voor gebeurtenissen die aangeven welke items van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen worden gebruikt om de geaggregeerde status van de entiteit te evalueren, ongeacht het filter.

### <a name="api"></a>API
Als u de replicastatus via `FabricClient` de API wilt krijgen, maakt u een en roept u de [GetReplicaHealthAsync-methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) aan op de HealthManager. Als u geavanceerde parameters wilt opgeven, gebruikt u [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
De cmdlet om de replica status te krijgen is [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Maak eerst verbinding met het cluster met de [cmdlet Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Met de volgende cmdlet wordt de status van de primaire replica voor alle partities van de service:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
U replicastatus krijgen met een [GET-verzoek](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) of een [post-verzoek](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) met gezondheidsbeleid dat in het lichaam is beschreven.

## <a name="get-deployed-application-health"></a>Geïmplementeerde toepassingsstatus krijgen
Geeft als resultaat de status van een toepassing die is geïmplementeerd op een entiteit met een knooppunt. Het bevat de statusstatussen van het geïmplementeerde servicepakket. Invoer:

* [Vereist] De toepassingsnaam (URI) en de naam van het knooppunt (tekenreeks) die de geïmplementeerde toepassing identificeren.
* [Optioneel] Het toepassingsstatusbeleid dat wordt gebruikt om het toepassingsmanifestbeleid te overschrijven.
* [Optioneel] Filters voor gebeurtenissen en geïmplementeerde servicepakketten die aangeven welke vermeldingen van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen en geïmplementeerde servicepakketten worden gebruikt om de geaggregeerde status van de entiteit te evalueren, ongeacht het filter.
* [Optioneel] Filter om gezondheidsstatistieken uit te sluiten. Als dit niet is opgegeven, wordt in de status van de status van de status van de status van de status van de status van de status van de status van ok, waarschuwing en fout weergegeven.

### <a name="api"></a>API
Als u de status van een toepassing wilt implementeren op `FabricClient` een knooppunt via de API, maakt u een en roept u de [getdeployedApplicationHealthAsync-methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) aan op de HealthManager. Als u optionele parameters wilt opgeven, gebruikt u [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
De cmdlet om de geïmplementeerde toepassingsstatus te krijgen is [Get-ServiceFabricDeployedApplicationHealth.](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) Maak eerst verbinding met het cluster met de [cmdlet Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) Als u wilt weten waar een toepassing is geïmplementeerd, voert u [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) uit en bekijkt u de geïmplementeerde toepassingkinderen.

De volgende cmdlet krijgt de status van de **stof:/WordCount-toepassing** geïmplementeerd op **_Node_2**.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
U de status van geïmplementeerde toepassingen krijgen met een [GET-aanvraag](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) of een [POST-verzoek](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) met gezondheidsbeleid dat in het lichaam is beschreven.

## <a name="get-deployed-service-package-health"></a>De status van het geïmplementeerde servicepakket openen
Retourneert de status van een geïmplementeerde entiteit voor servicepakketten. Invoer:

* [Vereist] De naam van de toepassing (URI), knooppuntnaam (tekenreeks) en de naam van het servicemanifest (tekenreeks) die het geïmplementeerde servicepakket identificeren.
* [Optioneel] Het toepassingsstatusbeleid dat wordt gebruikt om het toepassingsmanifestbeleid te overschrijven.
* [Optioneel] Filters voor gebeurtenissen die aangeven welke items van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen worden gebruikt om de geaggregeerde status van de entiteit te evalueren, ongeacht het filter.

### <a name="api"></a>API
Als u de status van een geïmplementeerd servicepakket `FabricClient` via de API wilt krijgen, maakt u een en roept u de [getdeployedServicePackageHealthAsync-methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) aan op zijn HealthManager. Als u optionele parameters wilt opgeven, gebruikt u [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
De cmdlet om de geïmplementeerde servicepakketstatus te krijgen is [Get-ServiceFabricDeployedServicePackageHealth.](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth) Maak eerst verbinding met het cluster met de [cmdlet Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) Als u wilt zien waar een toepassing is geïmplementeerd, voert u [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) uit en bekijkt u de geïmplementeerde toepassingen. Als u wilt zien welke servicepakketten zich in een toepassing bevinden, raadpleegt u het geïmplementeerde servicepakket dat kinderen in de [uitvoer van Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) gebruiken.

De volgende cmdlet krijgt de status van het **WordCountServicePkg-servicepakket** van de **fabric:/WordCount-toepassing** die op **_Node_2**wordt geïmplementeerd. De entiteit heeft **System.Hosting-rapporten** voor succesvolle service-pakket en entry-point activering, en succesvolle service-type registratie.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
U de status van het geïmplementeerde servicepakket krijgen met een [GET-aanvraag](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) of een [POST-verzoek](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) met gezondheidsbeleid dat in het lichaam is beschreven.

## <a name="health-chunk-queries"></a>Query's voor het somwerk over de gezondheid
De query's voor het zorgverlenersblok kunnen clusterkinderen op meerdere niveaus (recursief) per invoerfilters retourneren. Het ondersteunt geavanceerde filters die veel flexibiliteit in het kiezen van de kinderen om terug te keren. De filters kunnen kinderen opgeven aan de andere kant-en-id of door andere groeps-id's en/of statussen. Standaard worden er geen kinderen opgenomen, in tegenstelling tot gezondheidsopdrachten die altijd kinderen op het eerste niveau bevatten.

De [statusquery's retourneren](service-fabric-view-entities-aggregated-health.md#health-queries) alleen kinderen op het eerste niveau van de opgegeven entiteit per vereiste filters. Om de kinderen van de kinderen te krijgen, moet u aanvullende gezondheids-API's bellen voor elke entiteit van belang. Om de status van specifieke entiteiten te krijgen, moet u één status-API aanroepen voor elke gewenste entiteit. Met de geavanceerde filtering van de chunk-query u meerdere items aanvragen die van belang zijn voor één query, waardoor de grootte van het bericht en het aantal berichten worden geminimaliseerd.

De waarde van de chunk-query is dat u de status krijgen voor meer clusterentiteiten (mogelijk alle clusterentiteiten die beginnen bij vereiste root) in één gesprek. U complexe statusquery's uitdrukken, zoals:

* Retourneer alleen toepassingen in fout, en voor deze toepassingen omvatten alle services in waarschuwing of fout. Voor geretourneerde services u alle partities opnemen.
* Retourneer alleen de status van vier toepassingen, opgegeven door hun naam.
* Retourneer alleen de status van toepassingen van een gewenst toepassingstype.
* Retourneer alle geïmplementeerde entiteiten op een knooppunt. Retourneert alle toepassingen, alle geïmplementeerde toepassingen op het opgegeven knooppunt en alle geïmplementeerde servicepakketten op dat knooppunt.
* Retourneer ten onrechte alle replica's. Retourneert ten onrechte alle toepassingen, services, partities en alleen replica's.
* Retourneer alle toepassingen. Neem voor een bepaalde service alle partities op.

Momenteel wordt de query voor de statuschunk alleen weergegeven voor de clusterentiteit. Het retourneert een clusterstatusstuk, dat het:

* De geaggregateerde status van het cluster.
* De lijst met knooppunten met statusstatussen die invoerfilters respecteren.
* De lijst met diktoepassingen voor statusstatussen die invoerfilters respecteren. Elke groep toepassingsstatus bevat een chunk-lijst met alle services die invoerfilters respecteren en een lijst met alle geïmplementeerde toepassingen die de filters respecteren. Hetzelfde geldt voor de kinderen van diensten en geïmplementeerde toepassingen. Op deze manier kunnen alle entiteiten in het cluster mogelijk worden geretourneerd op een hiërarchische manier.

### <a name="cluster-health-chunk-query"></a>Chunk-query clusterstatus
Geeft als resultaat de status van de clusterentiteit en bevat de hiërarchische statussegmenten van vereiste kinderen. Invoer:

* [Optioneel] Het clusterstatusbeleid dat wordt gebruikt om de knooppunten en de clustergebeurtenissen te evalueren.
* [Optioneel] De beleidskaart voor toepassingsstatus, met het gezondheidsbeleid dat wordt gebruikt om het toepassingsmanifestbeleid te overschrijven.
* [Optioneel] Filters voor knooppunten en toepassingen die aangeven welke items van belang zijn en moeten worden geretourneerd in het resultaat. De filters zijn specifiek voor een entiteit/groep entiteiten of zijn van toepassing op alle entiteiten op dat niveau. De lijst met filters kan één algemeen filter en/of filters bevatten voor specifieke id's voor fijnkorrelige entiteiten die door de query worden geretourneerd. Als ze leeg zijn, worden de kinderen niet standaard teruggestuurd.
  Lees meer over de filters bij [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) en [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). De toepassingsfilters kunnen geavanceerde filters voor kinderen opnieuw opgeven.

Het brokresultaat omvat de kinderen die de filters respecteren.

Momenteel retourneert de chunk-query geen ongezonde evaluaties of entiteitsgebeurtenissen. Die extra informatie kan worden verkregen met behulp van de bestaande clusterstatusquery.

### <a name="api"></a>API
Als u clusterstatuschunk `FabricClient` wilt krijgen, maakt u een [getClusterHealthChunkAsync-methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) en roept u de methode GetClusterHealthChunkAsync aan op zijn **HealthManager**. U in [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) doorgaan om het gezondheidsbeleid en geavanceerde filters te beschrijven.

De volgende code krijgt clusterstatus chunk met geavanceerde filters.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
De cmdlet om de clusterstatus te krijgen is [Get-ServiceFabricClusterChunkHealth.](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk) Maak eerst verbinding met het cluster met de [cmdlet Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

De volgende code krijgt alleen knooppunten als ze fout zijn, behalve voor een specifiek knooppunt, dat altijd moet worden geretourneerd.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

De volgende cmdlet krijgt clusterbrok met toepassingsfilters.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1

                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1

                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5

                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

Met de volgende cmdlet worden alle geïmplementeerde entiteiten op een knooppunt geretourneerd.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
U clusterstatuschunk krijgen met een [GET-aanvraag](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) of een [POST-verzoek](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) met gezondheidsbeleid en geavanceerde filters die in het lichaam zijn beschreven.

## <a name="general-queries"></a>Algemene query's
Algemene query's retourneren een lijst met servicefabric-entiteiten van een bepaald type. Ze worden blootgesteld via de API (via de methoden op **FabricClient.QueryManager),** PowerShell-cmdlets en REST. Deze query's voegen subquery's uit meerdere componenten samen. Een daarvan is het [statusarchief](service-fabric-health-introduction.md#health-store), dat de geaggregeerde status voor elk queryresultaat vult.  

> [!NOTE]
> Algemene query's geven de geaggregeerde status van de entiteit weer en bevatten geen uitgebreide statusgegevens. Als een entiteit niet gezond is, u gezondheidsquery's opvolgen om alle gezondheidsinformatie te krijgen, inclusief gebeurtenissen, statussen van kindergezondheid en ongezonde evaluaties.
>
>

Als algemene query's een onbekende status voor een entiteit retourneren, is het mogelijk dat het statusarchief geen volledige gegevens over de entiteit heeft. Het is ook mogelijk dat een subquery naar de gezondheidswinkel niet succesvol is geweest (er was bijvoorbeeld een communicatiefout of de statusopslag werd beperkt). Follow-up met een statusquery voor de entiteit. Als er tijdelijke fouten in de subquery zijn opgetreden, zoals netwerkproblemen, kan deze vervolgquery slagen. Het kan u ook meer details geven van de health store over waarom de entiteit niet wordt blootgesteld.

De query's die **HealthState** voor entiteiten bevatten, zijn:

* Knooppuntlijst: geeft als resultaat de lijstknooppunten in het cluster (oppiepen).
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Toepassingslijst: geeft als resultaat de lijst met toepassingen in het cluster (oppiepen).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Servicelijst: geeft als resultaat de lijst met services in een toepassing (oppiepen).
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Partitielijst: geeft als resultaat de lijst met partities in een service (oppiepen).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Replicalijst: geeft als resultaat de lijst met replica's in een partitie (oppiepen).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Geïmplementeerde toepassingslijst: retourneert de lijst met geïmplementeerde toepassingen op een knooppunt.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Lijst met geïmplementeerde servicepakketten: retourneert de lijst met servicepakketten in een geïmplementeerde toepassing.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Sommige query's geven paginaresultaten weer. Het retourneren van deze query's is een lijst die is afgeleid van [PagedList\<T->](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Als de resultaten niet in een bericht passen, wordt alleen een pagina geretourneerd en een ContinuationToken die bijhoudt waar de opsomming is gestopt. Ga door met het aanroepen van dezelfde query en geef het vervolgtoken van de vorige query door om volgende resultaten te krijgen.

### <a name="examples"></a>Voorbeelden
De volgende code krijgt de ongezonde toepassingen in het cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

De volgende cmdlet krijgt de toepassingsdetails voor de fabric:/WordCount-toepassing. Merk op dat de gezondheidstoestand is op waarschuwing.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

De volgende cmdlet krijgt de services met een status van fout:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Cluster- en toepassingsupgrades
Tijdens een bewaakte upgrade van het cluster en de toepassing controleert Service Fabric de status om ervoor te zorgen dat alles gezond blijft. Als een entiteit niet in orde is zoals geëvalueerd met behulp van geconfigureerde statusbeleidsregels, past de upgrade upgradespecifiek beleid toe om de volgende actie te bepalen. De upgrade kan worden onderbroken om gebruikersinteractie toe te staan (zoals het herstellen van foutvoorwaarden of het wijzigen van beleid), of het kan automatisch worden teruggedraaid naar de vorige goede versie.

Tijdens een *clusterupgrade* u de status van de clusterupgrade krijgen. De upgradestatus bevat ongezonde evaluaties, die wijzen op wat niet in orde is in het cluster. Als de upgrade wordt teruggedraaid vanwege gezondheidsproblemen, onthoudt de upgradestatus de laatste ongezonde redenen. Deze informatie kan beheerders helpen te onderzoeken wat er mis ging nadat de upgrade is teruggedraaid of gestopt.

Tijdens een *upgrade van* een toepassing worden eventuele ongezonde evaluaties opgenomen in de status van de upgrade van de toepassing.

Als volgt wordt de status van de upgrade van de toepassing voor een gewijzigde fabric weergegeven:/WordCount-toepassing. Een waakhond meldde een fout op een van de replica's. De upgrade wordt teruggedraaid omdat de gezondheidscontroles niet worden gerespecteerd.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Lees meer over de upgrade van de [Service Fabric-applicatie.](service-fabric-application-upgrade.md)

## <a name="use-health-evaluations-to-troubleshoot"></a>Gezondheidsevaluaties gebruiken om problemen op te lossen
Wanneer er een probleem is met het cluster of een toepassing, kijk dan naar het cluster of de toepassingsstatus om te bepalen wat er mis is. De ongezonde evaluaties geven details over wat de huidige ongezonde status heeft geactiveerd. Als het nodig is, u inzoomen op ongezonde onderliggende entiteiten om de hoofdoorzaak te identificeren.

Overweeg bijvoorbeeld een toepassing die niet in orde is omdat er een foutrapport op een van de replica's is. De volgende Powershell-cmdlet toont de ongezonde evaluaties:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.

                                            Error event: SourceId='MyWatchdog', Property='Memory'.

ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

U kijken naar de replica om meer informatie te krijgen:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.

HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> De ongezonde evaluaties geven de eerste reden weer waarom de entiteit is geëvalueerd in de huidige status. Er kunnen meerdere andere gebeurtenissen zijn die deze status activeren, maar ze worden niet weergegeven in de evaluaties. Voor meer informatie zoomt u in op de gezondheidsentiteiten om alle ongezonde rapporten in het cluster te achterhalen.
>
>

## <a name="next-steps"></a>Volgende stappen
[Systeemstatusrapporten gebruiken om fouten op te lossen](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Aangepaste statusrapporten van servicefabric toevoegen](service-fabric-report-health.md)

[Hoe de servicestatus te rapporteren en te controleren](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Services lokaal controleren en een diagnose uitvoeren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade van servicefabric-toepassingen](service-fabric-application-upgrade.md)
