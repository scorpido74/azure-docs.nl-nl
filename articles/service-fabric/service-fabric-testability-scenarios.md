---
title: Chaos- en failovertests maken voor Azure Service Fabric
description: Met behulp van de Service Fabric chaos test en failover test scenario's om fouten te induceren en de betrouwbaarheid van uw diensten te controleren.
author: motanv
ms.topic: conceptual
ms.date: 10/1/2019
ms.author: motanv
ms.openlocfilehash: 206b02024ad052a12e87cfdf1773815027e8aec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465534"
---
# <a name="testability-scenarios"></a>Testabiliteitsscenario's
Grote gedistribueerde systemen zoals cloudinfrastructuren zijn inherent onbetrouwbaar. Azure Service Fabric biedt ontwikkelaars de mogelijkheid om services te schrijven die kunnen worden uitgevoerd bovenop onbetrouwbare infrastructuren. Om diensten van hoge kwaliteit te kunnen schrijven, moeten ontwikkelaars dergelijke onbetrouwbare infrastructuur kunnen induceren om de stabiliteit van hun diensten te testen.

De Fault Analysis Service geeft ontwikkelaars de mogelijkheid om foutacties te induceren om services te testen in aanwezigheid van storingen. Echter, gerichte gesimuleerde fouten krijgt u alleen tot nu toe. Om de tests verder te brengen, u de testscenario's in Service Fabric gebruiken: een chaostest en een failovertest. Deze scenario's simuleren continue tussenvallende fouten, zowel sierlijk als onsierlijk, gedurende langere tijd in het cluster. Zodra een test is geconfigureerd met de snelheid en het soort fouten, kan deze worden gestart via C# API's of PowerShell, om fouten in het cluster en uw service te genereren.

> [!WARNING]
> ChaosTestScenario wordt vervangen door een veerkrachtiger, op service gebaseerde Chaos. Raadpleeg het nieuwe artikel [Controlled Chaos](service-fabric-controlled-chaos.md) voor meer informatie.
> 
> 

## <a name="chaos-test"></a>Chaostest
Het chaosscenario genereert fouten in het hele cluster Service Fabric. Het scenario comprimeert fouten over het algemeen gezien in maanden of jaren tot een paar uur. De combinatie van interleaved fouten met de hoge foutsnelheid vindt hoek gevallen die anders worden gemist. Dit leidt tot een aanzienlijke verbetering van de codekwaliteit van de service.

### <a name="faults-simulated-in-the-chaos-test"></a>Fouten gesimuleerd in de chaos test
* Een knooppunt opnieuw starten
* Een geïmplementeerd codepakket opnieuw starten
* Een replica verwijderen
* Een replica opnieuw starten
* Een primaire replica verplaatsen (optioneel)
* Een secundaire replica verplaatsen (optioneel)

De chaostest voert meerdere iteraties van fouten en clustervalidaties uit voor de opgegeven periode. De tijd die wordt besteed aan het cluster om te stabiliseren en om validatie te laten slagen, is ook configureerbaar. Het scenario mislukt wanneer u één fout in clustervalidatie raakt.

Overweeg bijvoorbeeld een testset die een uur wordt uitgevoerd met maximaal drie gelijktijdige fouten. De test zal leiden tot drie fouten, en vervolgens valideren van de cluster status. De test zal herhalen door de vorige stap tot het cluster ongezond wordt of een uur voorbij gaat. Als het cluster in een iteratie ongezond wordt, d.w.z. het stabiliseert niet binnen een geconfigureerde tijd, mislukt de test met een uitzondering. Deze uitzondering geeft aan dat er iets is misgegaan en moet verder worden onderzocht.

In zijn huidige vorm veroorzaakt de storingsgeneratiemotor in de chaostest alleen veilige fouten. Dit betekent dat bij afwezigheid van externe fouten nooit een quorum of gegevensverlies zal optreden.

### <a name="important-configuration-options"></a>Belangrijke configuratieopties
* **TimeToRun**: Totale tijd dat de test zal worden uitgevoerd voordat u eindigt met succes. De test kan eerder worden voltooid in plaats van een validatiefout.
* **MaxClusterStabilizationTimeout**: Maximale hoeveelheid tijd om te wachten tot het cluster gezond is voordat de test mislukt. De uitgevoerde controles zijn of de clusterstatus ok is, de servicestatus is OK, de beoogde replicasetgrootte wordt bereikt voor de servicepartitie en er bestaan geen InBuild-replica's.
* **MaxConcurrentFaults**: Maximaal aantal gelijktijdige fouten veroorzaakt in elke iteratie. Hoe hoger het getal, hoe agressiever de test, wat resulteert in complexere failovers en overgangscombinaties. De test garandeert dat er bij afwezigheid van externe fouten geen quorum of gegevensverlies zal zijn, ongeacht hoe hoog deze configuratie is.
* **EnableMoveReplicaFaults:** Hiermee worden de fouten in- of uitgeschakeld die de verplaatsing van de primaire of secundaire replica's veroorzaken. Deze fouten zijn standaard uitgeschakeld.
* **WaitTimeBetweenIteraties**: Hoeveelheid tijd om te wachten tussen iteraties, d.w.z. na een ronde van fouten en bijbehorende validatie.

### <a name="how-to-run-the-chaos-test"></a>Hoe de chaostest uit te voeren
C#-voorbeeld

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

De Service Fabric Powershell-module bevat twee manieren om een chaosscenario te beginnen. `Invoke-ServiceFabricChaosTestScenario`is client-based, en als de client machine wordt afgesloten halverwege de test, geen verdere fouten zullen worden ingevoerd. Als alternatief is er een reeks opdrachten bedoeld om de test draaiende te houden in het geval van het afsluiten van de machine. `Start-ServiceFabricChaos`maakt gebruik van een stateful en betrouwbare systeemservice genaamd FaultAnalysisService, zodat fouten blijven worden geïntroduceerd totdat de TimeToRun is ingeschakeld. `Stop-ServiceFabricChaos`kan worden gebruikt om het scenario `Get-ServiceFabricChaosReport` handmatig te stoppen en een rapport te verkrijgen. Zie voor meer informatie de [Azure Service Fabric Powershell-referentie](https://docs.microsoft.com/powershell/module/servicefabric/?view=azureservicefabricps) en [het induceren van gecontroleerde chaos in servicefabricclusters.](service-fabric-controlled-chaos.md)

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Failovertest
Het failovertestscenario is een versie van het chaostestscenario dat zich richt op een specifieke servicepartitie. Het test het effect van failover op een specifieke servicepartitie terwijl de andere services onaangetast blijven. Zodra deze is geconfigureerd met de doelpartitiegegevens en andere parameters, wordt het uitgevoerd als een client-side tool die c#API's of PowerShell gebruikt om fouten voor een servicepartitie te genereren. Het scenario wordt door een reeks gesimuleerde fouten en servicevalidatie gehesen, terwijl uw bedrijfslogica aan de zijkant wordt uitgevoerd om een werkbelasting te bieden. Een fout in de validatie van de service duidt op een probleem dat nader onderzoek nodig heeft.

### <a name="faults-simulated-in-the-failover-test"></a>Fouten gesimuleerd in de failovertest
* Opnieuw een geïmplementeerd codepakket opnieuw starten waarbij de partitie wordt gehost
* Een primaire/secundaire replica of een statusloze instantie verwijderen
* Een primaire secundaire replica opnieuw starten (als een service voortduurde)
* Een primaire replica verplaatsen
* Een secundaire replica verplaatsen
* De partitie opnieuw starten

De failovertest veroorzaakt een gekozen fout en voert vervolgens validatie uit op de service om de stabiliteit ervan te garanderen. De failover test induceert slechts een fout op een moment, in tegenstelling tot mogelijke meerdere fouten in de chaos test. Als de servicepartitie niet stabiliseert binnen de geconfigureerde time-out na elke fout, mislukt de test. De test veroorzaakt alleen veilige fouten. Dit betekent dat bij afwezigheid van externe fouten geen quorum of gegevensverlies optreedt.

### <a name="important-configuration-options"></a>Belangrijke configuratieopties
* **PartitionSelector**: Object Selector dat de partitie opgeeft die moet worden getarget.
* **TimeToRun**: Totale tijd dat de test wordt uitgevoerd voordat deze wordt uitgevoerd.
* **MaxServiceStabilizationTimeout**: Maximale hoeveelheid tijd om te wachten tot het cluster gezond is voordat de test mislukt. De uitgevoerde controles zijn of de servicestatus ok is, de beoogde replicasetgrootte wordt bereikt voor alle partities en er bestaan geen InBuild-replica's.
* **WaitTimeBetweenFaults**: Tijd om te wachten tussen elke fout en validatiecyclus.

### <a name="how-to-run-the-failover-test"></a>De failovertest uitvoeren
**C #**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**Powershell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
