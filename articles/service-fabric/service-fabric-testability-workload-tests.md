---
title: Fouten in Azure Service Fabric-apps simuleren
description: Meer informatie over hoe u uw Azure Service Fabric-services verharden tegen gracieuze en onsierlijke fouten.
author: anmolah
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: d3d9f6478336c59adb875bf21438d5ffa457b1d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645987"
---
# <a name="simulate-failures-during-service-workloads"></a>Fouten simuleren tijdens serviceworkloads
De testabiliteitsscenario's in Azure Service Fabric stellen ontwikkelaars in staat zich geen zorgen te maken over het omgaan met individuele fouten. Er zijn echter scenario's waarin een expliciete interleaving van clientworkload en fouten nodig kan zijn. Het interleaving van clientworkload en fouten zorgt ervoor dat de service daadwerkelijk enige actie uitvoert wanneer er een storing optreedt. Gezien de mate van controle die testabiliteit biedt, kunnen deze zich op precieze punten van de werkbelastinguitvoering bevinden. Deze inductie van fouten op verschillende toestanden in de toepassing kan bugs vinden en de kwaliteit verbeteren.

## <a name="sample-custom-scenario"></a>Voorbeeld van aangepast scenario
Deze test toont een scenario dat de zakelijke werklast interleaves met [sierlijke en onsierlijke mislukkingen](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions). De fouten moeten worden veroorzaakt in het midden van servicebewerkingen of berekenen voor de beste resultaten.

Laten we een voorbeeld van een service doorlopen die vier workloads blootlegt: A, B, C en D. Elke service komt overeen met een set werkstromen en kan compute, storage of een mix zijn. Omwille van de eenvoud, zullen we de belasting in ons voorbeeld abstraheren. De verschillende fouten die in dit voorbeeld worden uitgevoerd zijn:

* RestartNode: Onsierlijke fout om een machine opnieuw op te starten simuleren.
* Opnieuw opstartenCodePakket: onsierlijke fout om servicehostprocescrashes te simuleren.
* RemoveReplica: Sierlijke fout om het verwijderen van replica's te simuleren.
* MovePrimary: Sierlijke fout om replicabewegingen te simuleren die worden geactiveerd door de load balancer van servicefabric.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.TestManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.FaultManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.FaultManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.FaultManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.FaultManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```
