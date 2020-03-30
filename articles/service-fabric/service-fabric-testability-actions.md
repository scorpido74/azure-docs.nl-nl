---
title: Fouten in Azure-microservices simuleren
description: In dit artikel wordt gesproken over de testabiliteitsacties in Microsoft Azure Service Fabric.
author: motanv
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 4bdb00eec38addc0c9f88eba8b73185ec5721277
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282040"
---
# <a name="testability-actions"></a>Testabiliteitsacties
Om een onbetrouwbare infrastructuur te simuleren, biedt Azure Service Fabric u, de ontwikkelaar, manieren om verschillende real-world mislukkingen en statusovergangen te simuleren. Deze worden blootgesteld als testabiliteitsacties. De acties zijn de API's op laag niveau die een specifieke foutinjectie, statusovergang of validatie veroorzaken. Door deze acties te combineren, u uitgebreide testscenario's voor uw services schrijven.

Service Fabric biedt een aantal veelvoorkomende testscenario's die uit deze acties bestaan. We raden u ten zeerste aan deze ingebouwde scenario's te gebruiken, die zorgvuldig zijn gekozen om algemene statusovergangen en foutgevallen te testen. Acties kunnen echter worden gebruikt om aangepaste testscenario's te maken wanneer u dekking wilt toevoegen voor scenario's die nog niet onder de ingebouwde scenario's vallen of die op maat zijn gemaakt voor uw toepassing.

C # implementaties van de acties zijn te vinden in de System.Fabric.dll assemblage. De System Fabric PowerShell module is te vinden in de Microsoft.ServiceFabric.Powershell.dll assemblage. Als onderdeel van de runtime-installatie is de ServiceFabric PowerShell-module geïnstalleerd om gebruiksgemak mogelijk te maken.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Sierlijke versus onsierlijke foutacties
Testabiliteitsacties worden ingedeeld in twee grote buckets:

* Onsierlijke fouten: Deze fouten simuleren storingen zoals machine opnieuw opstarten en proces crashes. In dergelijke gevallen van fouten stopt de uitvoeringscontext van het proces abrupt. Dit betekent dat er geen opschoning van de status kan worden uitgevoerd voordat de toepassing opnieuw wordt opgestart.
* Sierlijke fouten: Deze fouten simuleren sierlijke acties zoals replica bewegingen en druppels geactiveerd door load balancing. In dergelijke gevallen krijgt de service een melding van de afsluiting en kan de status worden opgeschoond voordat deze wordt verlaten.

Voor een betere kwaliteit validatie, voer de service en zakelijke werkbelasting, terwijl het induceren van verschillende sierlijke en onsierlijke fouten. Onsierlijke fouten oefenen scenario's uit waarbij het serviceproces abrupt wordt afgesloten in het midden van een werkstroom. Hiermee wordt het herstelpad getest zodra de servicereplica is hersteld door Service Fabric. Dit helpt de consistentie van gegevens te testen en of de servicestatus correct wordt onderhouden na fouten. De andere set fouten (de sierlijke fouten) test dat de service correct reageert op replica's die worden verplaatst door Service Fabric. Hiermee wordt de afhandeling van annulering getest in de RunAsync-methode. De service moet controleren of het annuleringstoken wordt ingesteld, de status correct opslaan en de RunAsync-methode afsluiten.

## <a name="testability-actions-list"></a>Lijst met testabiliteitsacties
| Actie | Beschrijving | Beheerde API | PowerShell-cmdlet | Sierlijke / onsierlijke fouten |
| --- | --- | --- | --- | --- |
| CleanTestState (CleanTestState) |Hiermee verwijdert u alle teststatus van het cluster in het geval van een slechte uitschakeling van het testrijder. |CleanTestStateAsync |Remove-ServiceFabricTestState |Niet van toepassing |
| InvokeDataLoss |Leidt tot gegevensverlies in een servicepartitie. |Beroep opDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Sierlijke |
| Beroepopquorumloss |Hiermee wordt een bepaalde stateful servicepartitie in quorumverlies plaatst. |InvokeQuorumLossAsync |Invoke-serviceFabricQuorumLoss |Sierlijke |
| VerplaatsenPrimair |Hiermee verplaatst u de opgegeven primaire replica van een stateful service naar het opgegeven clusterknooppunt. |MovePrimaryAsync |Move-serviceFabricPrimaryReplica |Sierlijke |
| Beweegsecundair |Hiermee verplaatst u de huidige secundaire replica van een stateful service naar een ander clusterknooppunt. |MoveSecondaryAsync |Move-serviceFabricSecondaryReplica |Sierlijke |
| RemoveReplica |Simuleert een replicafout door een replica uit een cluster te verwijderen. Hiermee wordt de replica gesloten en wordt deze overgezet naar rol 'Geen', waardoor al de status van het cluster wordt verwijderd. |ReplicaAsync verwijderen |Remove-serviceFabricReplica |Sierlijke |
| PloyedCodePackage opnieuw starten |Simuleert een fout in het codepakketproces door een codepakket opnieuw op te starten dat is geïmplementeerd op een knooppunt in een cluster. Hiermee wordt het codepakketproces afgebroken, waarbij alle replica's van de gebruikersservice die in dat proces worden gehost, opnieuw worden gestart. |PloyedDeployedCodePackageAsync opnieuw starten |Start-ServiceFabricDeployedCodePackage opnieuw opstarten |Onsierlijk |
| Startknooppunt opnieuw starten |Simuleert een storing in het clusterknooppunt ServiceFabric door een knooppunt opnieuw op te starten. |StartNodeAsync opnieuw op |Opnieuw opstarten-ServiceFabricNode |Onsierlijk |
| Partitie opnieuw starten |Simuleert een scenario voor een black-out van een datacenter of clusterblack-out door sommige of alle replica's van een partitie opnieuw te starten. |PartitionAsync opnieuw opstarten |Restart-ServiceFabricPartition |Sierlijke |
| Herstartopnieuw starten |Simuleert een replicafout door een doorgaande replica in een cluster opnieuw op te starten, de replica te sluiten en deze vervolgens opnieuw te openen. |ReplicaAsync opnieuw starten |Opnieuw opstarten-serviceFabricReplica |Sierlijke |
| StartNode |Hiermee start u een knooppunt in een cluster dat al is gestopt. |StartNodeAsync |Start-ServiceFabricNode |Niet van toepassing |
| StopNode |Simuleert een knooppuntfout door een knooppunt in een cluster te stoppen. Het knooppunt blijft staan tot StartNode wordt gebeld. |StopNodeAsync |Stop-ServiceFabricNode |Onsierlijk |
| ValidateApplication ValidateApplication ValidateApplication ValidateApplication |Valideert de beschikbaarheid en status van alle Service Fabric-services binnen een toepassing, meestal na het veroorzaken van een fout in het systeem. |ApplicationAsync valideren |Test-serviceFabricApplication |Niet van toepassing |
| ValidateService |Valideert de beschikbaarheid en status van een Service Fabric-service, meestal na het veroorzaken van een fout in het systeem. |ValidateServiceAsync |Test-serviceFabricService |Niet van toepassing |

## <a name="running-a-testability-action-using-powershell"></a>Een testabiliteitsactie uitvoeren met PowerShell
In deze zelfstudie ziet u hoe u een testabiliteitsactie uitvoert met PowerShell. U leert hoe u een testabiliteitsactie uitvoert tegen een lokaal (één-doos)cluster of een Azure-cluster. Microsoft.Fabric.Powershell.dll--de Service Fabric PowerShell-module wordt automatisch geïnstalleerd wanneer u de Microsoft Service Fabric MSI installeert. De module wordt automatisch geladen wanneer u een PowerShell-prompt opent.

Zelfstudiesegmenten:

* [Een actie uitvoeren tegen een cluster met één vak](#run-an-action-against-a-one-box-cluster)
* [Een actie uitvoeren tegen een Azure-cluster](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Een actie uitvoeren tegen een cluster met één vak
Als u een testabiliteitsactie wilt uitvoeren tegen een lokaal cluster, maakt u eerst verbinding met het cluster en opent u de PowerShell-prompt in de beheerdersmodus. Laten we eens kijken naar de **Restart-ServiceFabricNode** actie.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Hier wordt de actie **Restart-ServiceFabricNode** uitgevoerd op een knooppunt met de naam "Node1". De voltooiingsmodus geeft aan dat deze niet moet controleren of de actie opnieuw opstarten-knooppunt daadwerkelijk is geslaagd. Als u de voltooiingsmodus opgeeft als 'Verifiëren' wordt gecontroleerd of de actie voor opnieuw opstarten daadwerkelijk is gelukt. In plaats van het knooppunt rechtstreeks op te geven bij de naam, u het als volgt opgeven via een partitiesleutel en het soort replica:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** moet worden gebruikt om een Service Fabric-knooppunt opnieuw op te starten in een cluster. Hiermee wordt het Fabric.exe-proces gestopt, dat alle systeemservice- en gebruikersservicereplica's die op dat knooppunt worden gehost, opnieuw start. Het gebruik van deze API om uw service te testen helpt bij het ontdekken van bugs langs de failoverherstelpaden. Het helpt knooppuntfouten in het cluster te simuleren.

De volgende schermafbeelding toont de opdracht **Opnieuw opstarten-ServiceFabricNode** testability in actie.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

De output van de eerste **Get-ServiceFabricNode** (een cmdlet van de Service Fabric PowerShell-module) laat zien dat het lokale cluster vijf knooppunten heeft: Node.1 naar Node.5. Nadat de testabiliteitsactie (cmdlet) **Restart-ServiceFabricNode** is uitgevoerd op het knooppunt, genaamd Node.4, zien we dat de uptime van het knooppunt is gereset.

### <a name="run-an-action-against-an-azure-cluster"></a>Een actie uitvoeren tegen een Azure-cluster
Het uitvoeren van een testabiliteitsactie (met PowerShell) tegen een Azure-cluster is vergelijkbaar met het uitvoeren van de actie tegen een lokaal cluster. Het enige verschil is dat voordat u de actie uitvoeren, in plaats van verbinding te maken met het lokale cluster, u eerst verbinding moet maken met het Azure-cluster.

## <a name="running-a-testability-action-using-c35"></a>Een testabiliteitsactie uitvoeren met C-&#35;
Als u een testabiliteitsactie wilt uitvoeren met C#, moet u eerst verbinding maken met het cluster met FabricClient. Verkrijg vervolgens de parameters die nodig zijn om de actie uit te voeren. Verschillende parameters kunnen worden gebruikt om dezelfde actie uit te voeren.
Als u naar de actie RestartServiceFabricNode kijkt, u deze onder andere uitvoeren door de knooppuntgegevens (knooppuntnaam en knooppuntinstantie-id) in het cluster te gebruiken.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Parameteruitleg:

* **CompleteMode** geeft aan dat de modus niet moet controleren of de herstartactie daadwerkelijk is geslaagd. Als u de voltooiingsmodus opgeeft als 'Verifiëren' wordt gecontroleerd of de actie voor opnieuw opstarten daadwerkelijk is gelukt.  
* **In OperationTimeout** wordt de tijd ingesteld voordat de bewerking moet worden voltooid voordat een uitzondering op time-outuitzondering wordt gegenereerd.
* **Met CancellationToken** kan een in behandeling zijnde oproep worden geannuleerd.

In plaats van het knooppunt direct op te geven bij de naam, u het opgeven via een partitiesleutel en het soort replica.

Zie PartitionSelector en ReplicaSelector voor meer informatie.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>Partitiekiezer en replicakiezer
### <a name="partitionselector"></a>Partitiekiezer
PartitionSelector is een helper blootgesteld in testability en wordt gebruikt om een specifieke partitie waarop een van de testability acties uit te voeren selecteren. Het kan worden gebruikt om een specifieke partitie te selecteren als de partitie-ID vooraf bekend is. U de partitiesleutel ook leveren en de bewerking lost de partitie-id intern op. Je hebt ook de mogelijkheid om een willekeurige partitie te selecteren.

Als u deze helper wilt gebruiken, maakt u het object PartitionSelector en selecteert u de partitie met behulp van een van de methoden Selecteren*. Geef vervolgens het object PartitionSelector door aan de API die dit vereist. Als er geen optie is geselecteerd, wordt deze standaard ingesteld op een willekeurige partitie.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaKiezer
ReplicaSelector is een helper blootgesteld in testabiliteit en wordt gebruikt om te helpen bij het selecteren van een replica waarop een van de testabiliteit acties uit te voeren. Het kan worden gebruikt om een specifieke replica te selecteren als de replica-ID vooraf bekend is. Daarnaast heb je de mogelijkheid om een primaire replica of een willekeurige secundaire replica te selecteren. ReplicaSelector is afgeleid van PartitionSelector, dus u moet zowel de replica als de partitie selecteren waarop u de testabiliteitsbewerking wilt uitvoeren.

Als u deze helper wilt gebruiken, maakt u een object ReplicaSelector en stelt u de gewenste optie in om de replica en de partitie te selecteren. U het vervolgens doorgeven aan de API die dit vereist. Als er geen optie is geselecteerd, wordt deze standaard ingesteld op een willekeurige replica en willekeurige partitie.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Volgende stappen
* [Testabiliteitsscenario's](service-fabric-testability-scenarios.md)
* Uw service testen
  * [Fouten simuleren tijdens serviceworkloads](service-fabric-testability-workload-tests.md)
  * [Service-to-service-communicatiefouten](service-fabric-testability-scenarios-service-communication.md)

