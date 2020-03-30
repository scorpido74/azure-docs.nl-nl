---
title: Query voor clustergebeurtenissen met de GebeurtenisStore-API's
description: Meer informatie over het gebruik van de Azure Service Fabric EventStore API's om te zoeken naar platformgebeurtenissen
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 48350caef6bdaafda9aff7ac776d67b314aeaf8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614397"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Query EventStore API's voor clustergebeurtenissen

In dit artikel wordt ingaan op het opvragen van de EventStore-API's die beschikbaar zijn in Service Fabric-versie 6.2 en hoger - als u meer wilt weten over de EventStore-service, raadpleegt u het [EventStore-serviceoverzicht](service-fabric-diagnostics-eventstore.md). Momenteel heeft de EventStore-service alleen toegang tot gegevens van de afgelopen 7 dagen (dit is gebaseerd op het beleid voor het bewaren van diagnostische gegevens van uw cluster).

>[!NOTE]
>De EventStore-API's zijn GA vanaf Service Fabric-versie 6.4 voor alleen Windows-clusters die op Azure worden uitgevoerd.

De EventStore API's zijn rechtstreeks toegankelijk via een REST-eindpunt, of programmatisch. Afhankelijk van de query zijn er verschillende parameters nodig om de juiste gegevens te verzamelen. Deze parameters omvatten doorgaans:
* `api-version`: de versie van de EventStore API's die u gebruikt
* `StartTimeUtc`: definieert het begin van de periode waar u naar wilt kijken
* `EndTimeUtc`: einde van de periode

Naast deze parameters zijn er ook optionele parameters beschikbaar, zoals:
* `timeout`: de standaardtime-out van 60 seconden voor het uitvoeren van de aanvraagbewerking overschrijven
* `eventstypesfilter`: dit geeft u de mogelijkheid om te filteren op specifieke gebeurtenistypen
* `ExcludeAnalysisEvents`: niet terug 'Analyse' gebeurtenissen. Standaard worden EventStore-query's waar mogelijk weergegeven met "analysegebeurtenissen". Analysegebeurtenissen zijn rijkere operationele kanaalgebeurtenissen die extra context of informatie bevatten buiten een reguliere Service Fabric-gebeurtenis en meer diepte bieden.
* `SkipCorrelationLookup`: zoek niet naar potentiële gecorreleerde gebeurtenissen in het cluster. Standaard probeert de EventStore gebeurtenissen in een cluster te correleren en uw gebeurtenissen waar mogelijk aan elkaar te koppelen. 

Elke entiteit in een cluster kan query's zijn voor gebeurtenissen. U ook vragen naar gebeurtenissen voor alle entiteiten van het type. U bijvoorbeeld query's uitvoeren voor gebeurtenissen voor een specifiek knooppunt of voor alle knooppunten in uw cluster. De huidige set entiteiten waarvoor u vragen voor gebeurtenissen is (met de manier waarop de query zou worden gestructureerd):
* Cluster:`/EventsStore/Cluster/Events`
* Knooppunten:`/EventsStore/Nodes/Events`
* Knooppunt:`/EventsStore/Nodes/<NodeName>/$/Events`
* Toepassingen:`/EventsStore/Applications/Events`
* Toepassing:`/EventsStore/Applications/<AppName>/$/Events`
* Diensten:`/EventsStore/Services/Events`
* Service:`/EventsStore/Services/<ServiceName>/$/Events`
* Partities:`/EventsStore/Partitions/Events`
* Partitie:`/EventsStore/Partitions/<PartitionID>/$/Events`
* Replica 's:`/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Replica:`/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Wanneer u naar een toepassing of servicenaam verwijst, hoeft de query de 'stof:/' niet op te nemen Voorvoegsel. Als uw toepassing of servicenamen een "/" in zich hebben, schakelt u deze bovendien over naar een "~" om de query werkend te houden. Als uw toepassing bijvoorbeeld wordt weergegeven als 'fabric:/App1/FrontendApp', worden `/EventsStore/Applications/App1~FrontendApp/$/Events`uw app-specifieke query's gestructureerd als .
>Bovendien worden statusrapporten voor services vandaag weergegeven onder de bijbehorende toepassing, zodat u vragen naar gebeurtenissen voor `DeployedServiceHealthReportCreated` de juiste toepassingsentiteit. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>De EventStore opvragen via REST API-eindpunten

U de EventStore rechtstreeks bevragen via `GET` een `<your cluster address>/EventsStore/<entity>/Events/`REST-eindpunt, door aanvragen te doen voor:.

Bijvoorbeeld, om te vragen voor alle `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z`clustergebeurtenissen tussen en , uw aanvraag eruit ziet als volgt:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Dit kan geen gebeurtenissen retourneren of de lijst met gebeurtenissen die in json zijn geretourneerd:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

Hier kunnen we `2018-04-03T18:00:00Z` zien `2018-04-04T18:00:00Z`dat tussen en , dit cluster met succes `"CurrentClusterVersion": "0.0.0.0:"` `"TargetClusterVersion": "6.2:1.0"`zijn `"OverallUpgradeElapsedTimeInMs": "120196.5212"`eerste upgrade voltooid toen het voor het eerst werd opgestaan, van naar , in .

## <a name="query-the-eventstore-programmatically"></a>De EventStore programmatisch opvragen

U de EventStore ook programmatisch opvragen via de [clientbibliotheek servicestof.](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)

Zodra u uw Service Fabric-client hebt ingesteld, u vragen stellen naar gebeurtenissen door de EventStore als volgt te openen:`sfhttpClient.EventStore.<request>`

Hier is een voorbeeldaanvraag voor `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z`alle clustergebeurtenissen tussen en , via de `GetClusterEventListAsync` functie.

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

## <a name="sample-scenarios-and-queries"></a>Voorbeeldscenario's en query's

Hier volgen enkele voorbeelden over hoe u de API's voor gebeurtenisopslagrest bellen om de status van uw cluster te begrijpen.

*Clusterupgrades:*

Als u wilt zien wanneer uw cluster vorige week voor het laatst is bijgewerkt of heeft geprobeerd te worden bijgewerkt, u de API's voor onlangs voltooide upgrades voor uw cluster opvragen door de gebeurtenissen 'ClusterUpgradeCompleted' in de EventStore op te vragen:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problemen met clusterupgrades:*

Als er problemen zijn met een recente clusterupgrade, u ook vragen stellen voor alle gebeurtenissen voor de clusterentiteit. Je ziet verschillende gebeurtenissen, waaronder het starten van upgrades en elke UD waarvoor de upgrade succesvol is doorlopen. U ziet ook gebeurtenissen voor het punt waarop de rollback is gestart en bijbehorende gezondheidsgebeurtenissen. Hier is de query die u zou gebruiken voor deze:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Wijzigingen in de status van knooppunt:*

Als u de statuswijzigingen van uw knooppunt in de afgelopen dagen wilt zien - wanneer knooppunten omhoog of omlaag gingen of zijn geactiveerd of gedeactiveerd (door het platform, de chaosservice of door gebruikersinvoer) - gebruikt u de volgende query:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Toepassingsgebeurtenissen:*

U ook uw recente implementaties en upgrades van toepassingen bijhouden. Gebruik de volgende query om alle toepassingsgebeurtenissen in uw cluster te bekijken:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Historische gezondheid voor een toepassing:*

Naast alleen het zien van gebeurtenissen in de levenscyclus van toepassingen, u ook historische gegevens over de status van een specifieke toepassing zien. U dit doen door de toepassingsnaam op te geven waarvoor u de gegevens wilt verzamelen. Gebruik deze query om alle statusgebeurtenissen van de toepassing te krijgen: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`. Als u statusgebeurtenissen wilt opnemen die mogelijk zijn verlopen (voorbij de tijd `,ApplicationHealthReportExpired` om te leven (TTL)), voegt u aan het einde van de query toe om op twee soorten gebeurtenissen te filteren.

*Historische gezondheid voor alle diensten in "myApp":*

Momenteel worden statusrapportgebeurtenissen voor `DeployedServicePackageNewHealthReport` services weergegeven als gebeurtenissen onder de bijbehorende toepassingsentiteit. Gebruik de volgende query om te zien hoe uw services het hebben gedaan voor 'App1':`https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Partitieherconfiguratie:*

Als u alle partitiebewegingen wilt zien die in `PartitionReconfigured` uw cluster zijn gebeurd, zoekt u de gebeurtenis op. Dit kan u helpen erachter te komen welke workloads op welk knooppunt op specifieke tijdstippen, bij het diagnosticeren van problemen in uw cluster, zijn uitgevoerd. Hier is een voorbeeldquery die dat doet:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Chaos service:*

Er is een gebeurtenis voor wanneer de Chaos-service wordt gestart of gestopt die wordt blootgesteld op clusterniveau. Als u uw recente gebruik van de chaosservice wilt bekijken, gebruikt u de volgende query:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

