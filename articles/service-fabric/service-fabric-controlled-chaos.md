---
title: Chaos veroorzaken in de clusters van servicefabric's
description: Fault Injection en Cluster Analysis Service Service API's gebruiken om Chaos in het cluster te beheren.
author: motanv
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 37b451abd0a519dff17aba9b2d6c42b4762f30cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463169"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Gecontroleerde chaos in servicefabricclusters veroorzaken
Grootschalige gedistribueerde systemen zoals cloudinfrastructuren zijn inherent onbetrouwbaar. Azure Service Fabric stelt ontwikkelaars in staat om betrouwbare gedistribueerde services te schrijven bovenop een onbetrouwbare infrastructuur. Om robuuste gedistribueerde services bovenop een onbetrouwbare infrastructuur te schrijven, moeten ontwikkelaars de stabiliteit van hun services kunnen testen terwijl de onderliggende onbetrouwbare infrastructuur door gecompliceerde staatsovergangen gaat als gevolg van fouten.

De [Fault Injection and Cluster Analysis Service](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (ook bekend als de Fault Analysis Service) geeft ontwikkelaars de mogelijkheid om fouten te induceren om hun diensten te testen. Deze gerichte gesimuleerde fouten, zoals [het opnieuw opstarten van een partitie,](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps)kunnen helpen bij het uitoefenen van de meest voorkomende statusovergangen. Maar gerichte gesimuleerde fouten zijn per definitie bevooroordeeld en kunnen dus bugs missen die alleen worden weergegeven in moeilijk te voorspellen, lange en gecompliceerde volgorde van staatsovergangen. Voor een onbevooroordeelde test u Chaos gebruiken.

Chaos simuleert periodieke, tussenvallende fouten (zowel sierlijk als onsierlijk) in het cluster gedurende langere perioden. Een sierlijke fout bestaat uit een set van Service Fabric API-aanroepen, bijvoorbeeld, opnieuw opstarten replica fout is een sierlijke fout, omdat dit een nauwe gevolgd door een open op een replica. Verwijder replica, verplaats primaire replica, en verplaats secundaire replica zijn de andere sierlijke fouten uitgeoefend door Chaos. Onsierlijke fouten zijn procesuitgangen, zoals knooppunt opnieuw starten en het codepakket opnieuw starten. 

Zodra u Chaos hebt geconfigureerd met de snelheid en het soort fouten, u Chaos starten via C#, Powershell of REST API om fouten in het cluster en in uw services te genereren. U Chaos configureren om gedurende een bepaalde periode (bijvoorbeeld een uur) uit te voeren, waarna Chaos automatisch stopt, of u StopChaos API (C#, Powershell of REST) bellen om deze op elk gewenst moment te stoppen.

> [!NOTE]
> In zijn huidige vorm veroorzaakt Chaos alleen veilige fouten, wat impliceert dat bij afwezigheid van externe fouten een quorumverlies of gegevensverlies nooit optreedt.
>

Terwijl Chaos wordt uitgevoerd, produceert het verschillende gebeurtenissen die de stand van de run op dit moment vastleggen. Een ExecutingFaultsEvent bevat bijvoorbeeld alle fouten die Chaos in die iteratie heeft besloten uit te voeren. Een ValidationFailedEvent bevat de details van een validatiefout (status- of stabiliteitsproblemen) die is gevonden tijdens de validatie van het cluster. U de GetChaosReport API (C#, Powershell of REST) aanroepen om het rapport van Chaos te laten draaien. Deze gebeurtenissen worden gehandhaafd in een [betrouwbaar woordenboek](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), dat een afgekapt beleid heeft dat door twee configuraties wordt gedicteerd: **MaxStoredChaosEventCount** (standaardwaarde is 25000) en **StoredActionCleanupIntervalInSeconds** (standaardwaarde is 3600). Elke *StoredActionCleanupIntervalInSeconds* Chaos controleert en alle, behalve de meest recente *MaxStoredChaosEventCount* gebeurtenissen, worden verwijderd uit het betrouwbare woordenboek.

## <a name="faults-induced-in-chaos"></a>Fouten veroorzaakt in Chaos
Chaos genereert fouten in het gehele Cluster van de Structuur van de Dienst en comprimeert fouten die in maanden of jaren in een paar uren worden gezien. De combinatie van tussenvallen de fouten met de hoge foutsnelheid vindt hoek gevallen die anders kunnen worden gemist. Deze oefening van Chaos leidt tot een aanzienlijke verbetering van de codekwaliteit van de service.

Chaos veroorzaakt fouten uit de volgende categorieën:

* Een knooppunt opnieuw starten
* Een geïmplementeerd codepakket opnieuw starten
* Een replica verwijderen
* Een replica opnieuw starten
* Een primaire replica verplaatsen (configureerbaar)
* Een secundaire replica verplaatsen (configureerbaar)

Chaos draait in meerdere iteraties. Elke iteratie bestaat uit fouten en clustervalidatie voor de opgegeven periode. U de tijd die is besteed aan het cluster configureren om te stabiliseren en om de validatie te laten slagen. Als er een fout wordt gevonden in clustervalidatie, genereert en blijft Chaos een ValidationFailedEvent met de UTC-tijdstempel en de foutgegevens beheren en blijven bestaan. Denk bijvoorbeeld aan een instantie van Chaos die een uur moet worden uitgevoerd met maximaal drie gelijktijdige fouten. Chaos veroorzaakt drie fouten en valideert vervolgens de clusterstatus. Het doorloopt de vorige stap totdat het expliciet wordt gestopt via de StopChaosAsync API of een uur passen. Als het cluster ongezond wordt in een iteratie (dat wil zeggen, stabiliseert het niet of wordt het niet gezond binnen de doorgegeven MaxClusterStabilizationTimeout), genereert Chaos een ValidationFailedEvent. Deze gebeurtenis geeft aan dat er iets is misgegaan en mogelijk verder onderzoek nodig heeft.

Om te krijgen welke fouten Chaos veroorzaakt, u GetChaosReport API (powershell, C # of REST) gebruiken. De API krijgt het volgende segment van het Rapport Chaos op basis van het doorgegeven vervolgtoken of het doorgegeven tijdsbereik. U het Vervolgtoken opgeven om het volgende segment van het rapport Chaos te krijgen of u het tijdsbereik opgeven via StartTimeUtc en EndTimeUtc, maar u niet zowel het ContinuationToken als het tijdsbereik in hetzelfde gesprek opgeven. Wanneer er meer dan 100 Chaos-gebeurtenissen zijn, wordt het rapport Chaos geretourneerd in segmenten waarin een segment niet meer dan 100 Chaos-gebeurtenissen bevat.

## <a name="important-configuration-options"></a>Belangrijke configuratieopties
* **TimeToRun**: Totale tijd die Chaos draait voordat het eindigt met succes. U Chaos stoppen voordat deze is uitgevoerd voor de TimeToRun-periode via de StopChaos-API.

* **MaxClusterStabilizationTimeout**: de maximale hoeveelheid tijd om te wachten tot het cluster in orde is voordat u een ValidatieFailoutEvent produceert. Dit wachten is om de belasting op het cluster te verminderen terwijl het herstelt. De uitgevoerde controles zijn:
  * Als de clusterstatus in orde is
  * Als de servicestatus in orde is
  * Als de beoogde replicasetgrootte wordt bereikt voor de servicepartitie
  * Dat er geen InBuild replica's bestaan
* **MaxConcurrentFaults**: Het maximum aantal gelijktijdige fouten dat wordt veroorzaakt in elke iteratie. Hoe hoger het getal, hoe agressiever Chaos is en de failovers en de statusovergangscombinaties die het cluster doormaakt, zijn ook complexer. 

> [!NOTE]
> Ongeacht hoe hoog een waarde *MaxConcurrentFaults* heeft, chaos garandeert - bij afwezigheid van externe fouten - is er geen quorumverlies of verlies van gegevens.
>

* **EnableMoveReplicaFaults:** Hiermee worden de fouten in- of uitgeschakeld die ervoor zorgen dat de primaire of secundaire replica's worden verplaatst. Deze fouten zijn standaard ingeschakeld.
* **WaitTimeBetweenIterations:** De hoeveelheid tijd om te wachten tussen iteraties. Dat wil zeggen, de hoeveelheid tijd chaos zal pauzeren na het uitvoeren van een ronde van fouten en na het klaar met de bijbehorende validatie van de status van het cluster. Hoe hoger de waarde, hoe lager de gemiddelde foutinjectiesnelheid.
* **WaitTimeBetweenFaults**: De hoeveelheid tijd om te wachten tussen twee opeenvolgende fouten in een enkele iteratie. Hoe hoger de waarde, hoe lager de gelijktijdigheid van (of de overlap tussen) fouten.
* **ClusterHealthPolicy**: Clusterstatusbeleid wordt gebruikt om de status van het cluster tussen chaositeraties te valideren. Als de clusterstatus fout is of als er een onverwachte uitzondering optreedt tijdens het uitvoeren van fouten, wacht Chaos 30 minuten voordat de volgende statuscontrole wordt uitgevoerd - om het cluster enige tijd te geven om te herstellen.
* **Context**: een verzameling (tekenreeksen), typesleutelwaardeparen. De kaart kan worden gebruikt om informatie over de Chaos run op te nemen. Er kunnen niet meer dan 100 van dergelijke paren en elke string (toets of waarde) kan ten hoogste 4095 tekens lang. Deze kaart wordt ingesteld door de starter van de Chaos-run om optioneel de context over de specifieke run op te slaan.
* **ChaosTargetFilter**: Dit filter kan worden gebruikt om chaosfouten alleen te targeten op bepaalde knooppunttypen of alleen op bepaalde toepassingsinstanties. Als ChaosTargetFilter niet wordt gebruikt, maakt Chaos fouten in alle clusterentiteiten. Als ChaosTargetFilter wordt gebruikt, maakt Chaos alleen fouten bij de entiteiten die voldoen aan de ChaosTargetFilter-specificatie. NodeTypeInclusionList en ApplicationInclusionList staan alleen union semantiek toe. Met andere woorden, het is niet mogelijk om een kruising van NodeTypeInclusionList en ApplicationInclusionList op te geven. Het is bijvoorbeeld niet mogelijk om 'fout deze toepassing alleen op te geven wanneer deze op dat knooppunttype staat'. Zodra een entiteit is opgenomen in Een NodeTypeInclusionList of ApplicationInclusionList, kan die entiteit niet worden uitgesloten met ChaosTargetFilter. Zelfs als applicationX niet wordt weergegeven in ApplicationInclusionList, kan in sommige Chaos iteratie applicationX worden verweten omdat het toevallig op een knooppunt van nodeTypeY staat dat is opgenomen in NodeTypeInclusionList. Als zowel NodeTypeInclusionList als ApplicationInclusionList nietig of leeg zijn, wordt een ArgumentException gegooid.
    * **NodeTypeInclusionList:** een lijst met knooppunttypen die moeten worden opgenomen in chaosfouten. Alle soorten fouten (knooppunt opnieuw opstarten, codepakket opnieuw opstarten, replica verwijderen, replica opnieuw opstarten, primaire verplaatsen en secundair verplaatsen) zijn ingeschakeld voor de knooppunten van deze knooppunttypen. Als een nodetype (bijvoorbeeld NodeTypeX) niet in de NodeTypeInclusionList wordt weergegeven, worden nodelevel-fouten (zoals NodeRestart) nooit ingeschakeld voor de knooppunten van NodeTypeX, maar codepakket- en replicafouten kunnen nog steeds worden ingeschakeld voor NodeTypeX als een toepassing in de ApplicationInclusionList bevindt zich toevallig op een knooppunt van NodeTypeX. Maximaal 100 node type namen kunnen worden opgenomen in deze lijst, om dit aantal te verhogen, een config upgrade is vereist voor MaxNumberNumberNumberNumberNumberInChaosTargetFilter configuratie.
    * **ApplicationInclusionList**: Een lijst met toepassings-URI's die moeten worden opgenomen in chaosfouten. Alle replica's die behoren tot services van deze toepassingen zijn vatbaar voor replicafouten (replica opnieuw opstarten, replica verwijderen, primaire verplaatsen en secundaire verplaatsen) door Chaos. Chaos kan een codepakket alleen opnieuw starten als het codepakket alleen replica's van deze toepassingen host. Als een toepassing niet in deze lijst wordt weergegeven, kan deze nog steeds worden verweten in een chaos-iteratie als de toepassing op een knooppunt van een knooppunttype terechtkomt dat is opgenomen in NodeTypeInclusionList. Als applicationX echter is gekoppeld aan nodeTypeY door plaatsingsbeperkingen en applicationX afwezig is in ApplicationInclusionList en nodeTypeY afwezig is in NodeTypeInclusionList, zal applicationX nooit worden verweten. Maximaal 1000 applicatienamen kunnen in deze lijst worden opgenomen, om dit aantal te verhogen, is een config-upgrade vereist voor de MaxNumberNumberApplicationsInChaosTargetFilter-configuratie.

## <a name="how-to-run-chaos"></a>Chaos uitvoeren

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy.
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities.
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
