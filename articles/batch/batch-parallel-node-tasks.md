---
title: Taken parallel uitvoeren om reken resources te optimaliseren
description: Verhoog de efficiëntie en verlaag de kosten met minder reken knooppunten en voer gelijktijdige taken uit op elk knoop punt in een Azure Batch pool
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 3c3a81aa624ccc67c0f9e8ec23e5ef9b8e61c724
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850996"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Gelijktijdig taken uitvoeren om het gebruik van batch Compute-knoop punten te maximaliseren

Door meer dan één taak tegelijk uit te voeren op elk reken knooppunt in uw Azure Batch pool, kunt u het resource gebruik maximaliseren op een kleiner aantal knoop punten in de groep. Voor sommige werk belastingen kan dit leiden tot kortere taak tijden en lagere kosten.

Hoewel sommige scenario's van toepassing zijn op het reserveren van alle resources van een knoop punt voor één enkele taak, profiteren verschillende situaties van het toestaan van meerdere taken om deze resources te delen:

* Het **minimaliseren van gegevens overdracht** wanneer taken gegevens kunnen delen. In dit scenario kunt u de kosten voor gegevens overdracht aanzienlijk verminderen door gedeelde gegevens naar een kleiner aantal knoop punten te kopiëren en taken parallel uit te voeren op elk knoop punt. Dit is met name van toepassing als de gegevens die naar elk knoop punt moeten worden gekopieerd, moeten worden overgedragen tussen geografische regio's.
* **Geheugen gebruik maximaliseren** wanneer taken een grote hoeveelheid geheugen vereisen, maar alleen tijdens korte Peri Oden, en op variabele tijdstippen tijdens de uitvoering. U kunt minder, maar grotere, reken knooppunten met meer geheugen gebruiken om dergelijke pieken efficiënt af te handelen. Op deze knoop punten worden meerdere taken parallel uitgevoerd op elk knoop punt, maar elke taak zou gebruikmaken van het geheugen van de knoop punten op verschillende tijdstippen.
* **Beperken van knooppunt aantal limieten** bij communicatie tussen knoop punten in een groep. Op dit moment zijn groepen die zijn geconfigureerd voor communicatie tussen knoop punten beperkt tot 50 Compute-knoop punten. Als elk knoop punt in een dergelijke groep parallelle taken kan uitvoeren, kan een groter aantal taken tegelijkertijd worden uitgevoerd.
* **Repliceren van een on-premises Compute-Cluster**, zoals wanneer u een reken omgeving voor het eerst naar Azure verplaatst. Als uw huidige on-premises oplossing meerdere taken per Compute-knoop punt uitvoert, kunt u het maximum aantal knooppunt taken verhogen om deze configuratie beter te spie gelen.

## <a name="example-scenario"></a>Voorbeeldscenario
Als voor beeld van het illustreren van de voor delen van het uitvoeren van parallelle taken, laten we zien dat uw taak toepassing CPU-en geheugen vereisten heeft, zodat de [standaard \_ D1](../cloud-services/cloud-services-sizes-specs.md) -knoop punten voldoende zijn. Maar 1.000 van deze knoop punten zijn nodig om de taak in de vereiste tijd te volt ooien.

In plaats van gebruik te maken van standaard \_ D1-knoop punten met 1 CPU-kern, kunt u [standaard \_ D14](../cloud-services/cloud-services-sizes-specs.md) -knoop punten gebruiken met elke 16 kernen en de uitvoering van parallelle taken inschakelen. Daarom kunnen *16 keer minder knoop punten* worden gebruikt:--in plaats van 1.000-knoop punten, alleen 63 vereist. Als er bovendien grote toepassings bestanden of referentie gegevens voor elk knoop punt zijn vereist, zijn de duur en efficiëntie van de taak opnieuw verbeterd, omdat de gegevens slechts naar 63 knoop punten worden gekopieerd.

## <a name="enable-parallel-task-execution"></a>Uitvoering van parallelle taak inschakelen
U kunt reken knooppunten voor het uitvoeren van parallelle taken configureren op het groeps niveau. Stel in de batch .NET-bibliotheek de eigenschap [CloudPool. TaskSlotsPerNode][maxtasks_net] in wanneer u een pool maakt. Als u de batch-REST API gebruikt, stelt u het element [taskSlotsPerNode][rest_addpool] in de hoofd tekst van de aanvraag in tijdens het maken van de groep.

Met Azure Batch kunt u taak sleuven per knoop punt instellen op Maxi maal (4x) het aantal knooppunt kernen. Als de pool bijvoorbeeld is geconfigureerd met knoop punten met de grootte ' groot ' (vier kernen), `taskSlotsPerNode` kan deze worden ingesteld op 16. Ongeacht het aantal kernen dat het knoop punt heeft, kunt u Maxi maal 256 taak sleuven per knoop punt hebben. Zie [grootten voor Cloud Services](../cloud-services/cloud-services-sizes-specs.md)voor meer informatie over het aantal kernen voor elk van de groottes van knoop punten. Zie [quota's en limieten voor de Azure batch-service](batch-quota-limit.md)voor meer informatie over service limieten.

> [!TIP]
> Zorg ervoor dat u rekening moet houden met de `taskSlotsPerNode` waarde wanneer u een [formule voor automatisch schalen][enable_autoscaling] voor uw groep bouwt. Een formule die evalueert, kan bijvoorbeeld `$RunningTasks` aanzienlijk worden beïnvloed door een toename van taken per knoop punt. Zie [reken knooppunten automatisch schalen in een Azure batch groep](batch-automatic-scaling.md) voor meer informatie.
>
>

> [!NOTE]
> U kunt de `taskSlotsPerNode` eigenschap element en [TaskSlotsPerNode][maxtasks_net] alleen instellen tijdens het maken van de groep. Ze kunnen niet worden gewijzigd nadat er al een pool is gemaakt.
>
>

## <a name="distribution-of-tasks"></a>Distributie van taken
Wanneer de reken knooppunten in een pool gelijktijdig taken kunnen uitvoeren, is het belang rijk om op te geven hoe u wilt dat de taken worden gedistribueerd over de knoop punten in de pool.

Met de eigenschap [CloudPool. TaskSchedulingPolicy][task_schedule] kunt u opgeven dat taken gelijkmatig moeten worden toegewezen op alle knoop punten in de pool (' sprei ding '). U kunt ook opgeven dat er zoveel mogelijk taken moeten worden toegewezen aan elk knoop punt voordat taken worden toegewezen aan een ander knoop punt in de pool (' verpakking ').

Als voor beeld van hoe deze functie waardevol is, moet u rekening houden met de pool van [standaard \_ D14](../cloud-services/cloud-services-sizes-specs.md) -knoop punten (in het bovenstaande voor beeld) die is geconfigureerd met een [CloudPool. TaskSlotsPerNode][maxtasks_net] -waarde van 16. Als [CloudPool. TaskSchedulingPolicy][task_schedule] is geconfigureerd met een [ComputeNodeFillType][fill_type] - *pakket*, wordt het gebruik van alle 16 kernen van elk knoop punt gemaximaliseerd en kan een groep voor automatisch [schalen](batch-automatic-scaling.md) ongebruikte knoop punten uit de groep weghalen (knoop punten zonder toegewezen taken). Hiermee wordt het resource gebruik geminimaliseerd en bespaart u geld.

## <a name="variable-slots-per-task"></a>Variabele sleuven per taak
Taak kan worden gedefinieerd met de eigenschap [CloudTask. RequiredSlots][taskslots_net] om op te geven hoeveel sleuven er moeten worden uitgevoerd op een reken knooppunt, met een standaard waarde van 1. U kunt variabele taak sleuven instellen als uw taken verschillende weging hebben over het resource gebruik op het reken knooppunt, zodat elk reken knooppunt een redelijk aantal gelijktijdige actieve taken kan hebben zonder veel systeem bronnen, zoals CPU of geheugen.

Voor een pool met een eigenschap `taskSlotsPerNode = 8` kunt u bijvoorbeeld meerdere kernen CPU-intensieve taken verzenden met `requiredSlots = 8` , terwijl andere taken met zijn vereist `requiredSlots = 1` . Wanneer deze gemengde werk belasting is gepland voor de groep, worden de CPU-intensieve taken uitsluitend uitgevoerd op het reken knooppunt, terwijl andere taken gelijktijdig kunnen worden uitgevoerd (Maxi maal acht taken) op andere knoop punten. Dit helpt u om uw werk belasting te verdelen over reken knooppunten en de efficiëntie van het resource gebruik te verbeteren.

> [!TIP]
> Wanneer u variabelen taak sleuven gebruikt, is het mogelijk dat grote taken met meer vereiste sleuven tijdelijk niet kunnen worden gepland omdat er onvoldoende sleuven beschikbaar zijn op een reken knooppunt, zelfs wanneer er nog steeds niet-actieve sleuven op sommige knoop punten aanwezig zijn. U kunt de taak prioriteit voor deze taken verhogen om de kans te verg Roten om te concurreren voor beschik bare sleuven op knoop punten.
>
> De batch-service levert ook [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) wanneer het niet lukt om een taak uit te voeren, terwijl de planning opnieuw wordt uitgevoerd totdat de vereiste sleuven beschikbaar zijn. U kunt naar die gebeurtenis Luis teren om potentiële taak planningen te detecteren die achterblijvend zijn.
>

> [!NOTE]
> Geef de taak niet `requiredSlots` op die groter is dan de pool `taskSlotsPerNode` . Dit zorgt ervoor dat de taak nooit kan worden uitgevoerd. De validatie van de batch-service wordt niet uitgevoerd wanneer u taken indient, omdat de taak mogelijk geen pool heeft die is gebonden aan de indienings tijd, of moet worden gewijzigd in een andere groep door deze uit te scha kelen of opnieuw in te scha kelen.
>

## <a name="batch-net-example"></a>Batch .NET-voor beeld
In de volgende batch code fragmenten van [.net][api_net] API ziet u hoe u een pool met meerdere taak sleuven per knoop punt maakt en taak met vereiste sleuven verzendt.

### <a name="create-pool"></a>Groep maken
Dit code fragment toont een aanvraag voor het maken van een groep die vier knoop punten bevat met vier taak sleuven die per knoop punt zijn toegestaan. Hiermee wordt een beleid voor taak planning opgegeven waarmee elk knoop punt met taken wordt gevuld voordat taken worden toegewezen aan een ander knoop punt in de pool. Zie [BatchClient. pool Operations. CreatePool][poolcreate_net]voor meer informatie over het toevoegen van groepen met behulp van de batch .net-API.

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-task-with-required-slots"></a>Taak maken met vereiste sleuven
Met dit code fragment wordt een taak gemaakt met niet-standaard `requiredSlots` . Deze taak wordt alleen uitgevoerd als er voldoende vrije sleuven beschikbaar zijn op het reken knooppunt.
```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Reken knooppunten weer geven met aantallen voor het uitvoeren van taken en sleuven
In dit code fragment worden alle reken knooppunten in de groep weer gegeven en worden de aantallen voor het uitvoeren van taken en taak sleuven per knoop punt afgedrukt.
```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Aantal taken voor de taak weer geven
Met dit code fragment worden taak aantallen voor de taak opgehaald, inclusief het aantal taken en taken per taak.
```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch REST-voor beeld
Het fragment van de [batch rest][api_rest] API bevat een aanvraag voor het maken van een groep die twee grote knoop punten bevat met een maximum van vier taken per knoop punt. Zie [een pool toevoegen aan een account][rest_addpool]voor meer informatie over het toevoegen van groepen met behulp van de rest API.

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

Dit code fragment bevat een aanvraag om een taak toe te voegen die niet standaard is `requiredSlots` . Deze taak wordt alleen uitgevoerd als er voldoende vrije sleuven beschikbaar zijn op het reken knooppunt.
```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample"></a>Codevoorbeeld
Het [ParallelNodeTasks][parallel_tasks_sample] -project op github illustreert het gebruik van de eigenschap [CloudPool. TaskSlotsPerNode][maxtasks_net] .

Deze C#-console toepassing maakt gebruik van de [batch .net][api_net] -bibliotheek om een groep met een of meer reken knooppunten te maken. Er wordt een configureerbaar aantal taken op deze knoop punten uitgevoerd om variabele belasting te simuleren. De uitvoer van de toepassing geeft aan welke knoop punten elke taak uitvoeren. De toepassing biedt ook een samen vatting van de taak parameters en-duur. Hieronder ziet u het overzicht van de uitvoer van twee verschillende uitvoeringen van de voorbeeld toepassing.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

De eerste uitvoering van de voorbeeld toepassing laat zien dat met één knoop punt in de pool en de standaard instelling van één taak per knoop punt, de taak duur meer dan 30 minuten is.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

Bij de tweede uitvoering van het voor beeld ziet u een aanzienlijke afname van de taak duur. De reden hiervoor is dat de groep is geconfigureerd met vier taken per knoop punt, waardoor parallelle taken kunnen worden uitgevoerd om de taak in bijna een kwart van de tijd te volt ooien.

> [!NOTE]
> De taak duur in de bovenstaande samen vattingen bevat geen groeps aanmaak tijd. Elk van de bovenstaande taken is verzonden naar eerder gemaakte Pools waarvan de reken knooppunten de status *niet-actief* hadden tijdens het indienen.
>
>

## <a name="next-steps"></a>Volgende stappen
### <a name="batch-explorer-heat-map"></a>Heatmap Batch Explorer
[Batch Explorer][batch_labs] is een gratis, uitgebreid, zelfstandig client hulpprogramma waarmee Azure batch toepassingen kunnen worden gemaakt, opgespoord en gecontroleerd. Batch Explorer bevat een functie voor *heatmap* die de visualisatie van taak uitvoering biedt. Wanneer u de voorbeeld toepassing [ParallelTasks][parallel_tasks_sample] uitvoert, kunt u de functie heatmap gebruiken om eenvoudig de uitvoering van parallelle taken op elk knoop punt te visualiseren.


[api_net]: /dotnet/api/microsoft.azure.batch
[api_rest]: /rest/api/batchservice/
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[enable_autoscaling]: /rest/api/batchservice/pool/enableautoscale
[fill_type]: /dotnet/api/microsoft.azure.batch.common.computenodefilltype
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: /dotnet/api/microsoft.azure.batch.cloudpool
[rest_addpool]: /rest/api/batchservice/pool/add
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: /dotnet/api/microsoft.azure.batch.pooloperations
[task_schedule]: /dotnet/api/microsoft.azure.batch.cloudpool
[taskslots_net]: /dotnet/api/microsoft.azure.batch.cloudtask.requiredslots
