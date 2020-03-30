---
title: Taakafhankelijkheden maken om taken uit te voeren - Azure Batch
description: Taken maken die afhankelijk zijn van de voltooiing van andere taken voor het verwerken van MapReduce-stijl en vergelijkbare big data-workloads in Azure Batch.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca771117e889afc8e143c4ca4626ab2d3bb4da2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022899"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Taakafhankelijkheden maken om taken uit te voeren die afhankelijk zijn van andere taken

U taakafhankelijkheden definiëren om een taak of set taken uit te voeren nadat een bovenliggende taak is voltooid. Enkele scenario's waarin taakafhankelijkheden nuttig zijn, zijn:

* MapWorkloads in de cloud verminderen.
* Taken waarvan de gegevensverwerkingstaken kunnen worden uitgedrukt als een gerichte acyclische grafiek (DAG).
* Pre-rendering en post-rendering processen, waar elke taak moet voltooien voordat de volgende taak kan beginnen.
* Elke andere taak waarbij downstreamtaken afhankelijk zijn van de output van upstreamtaken.

Met batchtaakafhankelijkheden u taken maken die zijn gepland voor uitvoering op compute-knooppunten na het voltooien van een of meer bovenliggende taken. U bijvoorbeeld een taak maken die elk frame van een 3D-film met afzonderlijke, parallelle taken rendert. De laatste taak- de "samenvoegtaak"-- voegt de gerenderde frames pas samen in de volledige film nadat alle frames zijn gerenderd.

Afhankelijke taken worden standaard pas voor uitvoering gepland nadat de bovenliggende taak is voltooid. U een afhankelijkheidsactie opgeven om het standaardgedrag te overschrijven en taken uit te voeren wanneer de bovenliggende taak mislukt. Zie de sectie [Afhankelijkheidsacties](#dependency-actions) voor meer informatie.  

U taken maken die afhankelijk zijn van andere taken in een één-op-één- of één-op-één-relatie. U ook een bereikafhankelijkheid maken waarbij een taak afhankelijk is van de voltooiing van een groep taken binnen een bepaald taak-id. U deze drie basisscenario's combineren om veel-op-veel relaties te maken.

## <a name="task-dependencies-with-batch-net"></a>Taakafhankelijkheden met Batch .NET
In dit artikel bespreken we hoe u taakafhankelijkheden configureert met behulp van de [Batch .NET-bibliotheek.][net_msdn] We laten u eerst zien hoe u [taakafhankelijkheid](#enable-task-dependencies) van uw taken inschakelen en laten vervolgens zien hoe [u een taak configureren met afhankelijkheden.](#create-dependent-tasks) We beschrijven ook hoe u een afhankelijkheidsactie opgeeft om afhankelijke taken uit te voeren als de bovenliggende onderneming faalt. Tot slot bespreken we de [afhankelijkheidsscenario's](#dependency-scenarios) die Batch ondersteunt.

## <a name="enable-task-dependencies"></a>Taakafhankelijkheden inschakelen
Als u taakafhankelijkheden wilt gebruiken in uw Batch-toepassing, moet u eerst de taak configureren om taakafhankelijkheden te gebruiken. Schakel in Batch .NET het in op uw [CloudJob][net_cloudjob] `true`door de eigenschap [UsesTaskDependencies][net_usestaskdependencies] in te stellen op:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

In het voorgaande codefragment is 'batchClient' een instantie van de klasse [BatchClient.][net_batchclient]

## <a name="create-dependent-tasks"></a>Afhankelijke taken maken
Als u een taak wilt maken die afhankelijk is van de voltooiing van een of meer bovenliggende taken, u opgeven dat de taak afhankelijk is van de andere taken. Configureer in Batch .NET de [CloudTask][net_cloudtask]. [DependsOn][net_dependson] met een instantie van de klasse [Taakafhankelijkheden:][net_taskdependencies]

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

In dit codefragment wordt een afhankelijke taak gemaakt met taak-ID 'Bloemen'. De taak "Bloemen" is afhankelijk van taken "Regen" en "Zon". Taak "Bloemen" wordt alleen op een compute-knooppunt uitgevoerd nadat taken 'Regen' en 'Zon' zijn voltooid.

> [!NOTE]
> Standaard wordt een taak als voltooid beschouwd wanneer **completed** deze in de voltooide `0`status staat en de **exitcode** . In Batch .NET betekent dit een [CloudTask][net_cloudtask]. [Waarde van][net_taskstate] `Completed` de eigenschap State van en de [TaskExecutionInformation][net_taskexecutioninformation]van de CloudTask . [De][net_exitcode] waarde van `0`de eigenschap ExitCode is . Zie de sectie [Afhankelijkheidsacties](#dependency-actions) voor het wijzigen van dit artikel.
> 
> 

## <a name="dependency-scenarios"></a>Afhankelijkheidsscenario's
Er zijn drie basisscenario's voor taakafhankelijkheid die u gebruiken in Azure Batch: één-op-één, één-op-één en afhankelijkheid van taak-id-bereik. Deze kunnen worden gecombineerd om een vierde scenario te bieden, veel-op-veel.

| Scenario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Voorbeeld |  |
|:---:| --- | --- |
|  [Een-op-een](#one-to-one) |*taskB* is afhankelijk van *taskA* <p/> *taskB* wordt pas gepland voor uitvoering als *taskA* is voltooid |![Diagram: één-op-één taakafhankelijkheid][1] |
|  [Een-op-veel](#one-to-many) |*taskC* is afhankelijk van zowel *taskA* als *taskB* <p/> *taskC* wordt pas gepland voor uitvoering als zowel *taskA* als *taskB* met succes zijn voltooid |![Diagram: één-op-veel taakafhankelijkheid][2] |
|  [Taak-id-bereik](#task-id-range) |*taskD* is afhankelijk van een reeks taken <p/> *taakD* wordt pas gepland voor uitvoering als de taken met iD's *1* tot en met *10* zijn voltooid |![Diagram: Afhankelijkheid taak-id-bereik][3] |

> [!TIP]
> U **veel-op-veel** relaties maken, zoals waar taken C, D, E en F elk afhankelijk zijn van taken A en B. Dit is bijvoorbeeld handig in parallelle voorbewerkingsscenario's waarbij uw downstreamtaken afhankelijk zijn van de uitvoer van meerdere upstreamtaken.
> 
> In de voorbeelden in deze sectie wordt een afhankelijke taak pas uitgevoerd nadat de bovenliggende taken zijn voltooid. Dit gedrag is het standaardgedrag voor een afhankelijke taak. U een afhankelijke taak uitvoeren nadat een bovenliggende taak is mislukt door een afhankelijkheidsactie op te geven om het standaardgedrag te overschrijven. Zie de sectie [Afhankelijkheidsacties](#dependency-actions) voor meer informatie.

### <a name="one-to-one"></a>Een-op-een
In een één-op-één relatie is een taak afhankelijk van de succesvolle voltooiing van een bovenliggende taak. Als u de afhankelijkheid wilt maken, geeft u één taak-id op aan de [taakafhankelijkheden][net_taskdependencies]. Statische methode [onId][net_onid] wanneer u de eigenschap [DependsOn][net_dependson] van [CloudTask][net_cloudtask]vult .

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Een-op-veel
In een één-op-veel relatie is een taak afhankelijk van de voltooiing van meerdere bovenliggende taken. Als u de afhankelijkheid wilt maken, verstrekt u een verzameling taak-id's aan de [taakafhankelijkheden][net_taskdependencies]. Statische methode [OnIds][net_onids] wanneer u de eigenschap [DependsOn][net_dependson] van [CloudTask][net_cloudtask]vult .

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>Taak-id-bereik
In een afhankelijkheid van een reeks bovenliggende taken is een taak afhankelijk van de voltooiing van taken waarvan de id's binnen een bereik liggen.
Als u de afhankelijkheid wilt maken, geeft u de eerste en laatste taak-id's in het bereik op aan de [taakafhankelijkheden][net_taskdependencies]. Statische methode [OnIdRange][net_onidrange] wanneer u de eigenschap [DependsOn][net_dependson] van [CloudTask][net_cloudtask]invult .

> [!IMPORTANT]
> Wanneer u taak-ID-bereiken gebruikt voor uw afhankelijkheden, worden alleen taken met id's die gehele waarden vertegenwoordigen, geselecteerd op het bereik. Dus het `1..10` bereik `3` selecteert `7`taken `5flamingoes`en , maar niet . 
> 
> Het voorlopen van nullen is niet significant bij het `4` `04` evalueren `004` van afhankelijkheden van het bereik, dus `4`taken met tekenreeks-id's en bevinden zich allemaal *binnen* het bereik en worden allemaal als taak behandeld, dus de eerste die wordt voltooid, voldoet aan de afhankelijkheid.
> 
> Elke taak in het bereik moet voldoen aan de afhankelijkheid, hetzij door het voltooien met succes of door te voltooien met een fout die is toegewezen aan een afhankelijkheidactie ingesteld **op Voldoen**. Zie de sectie [Afhankelijkheidsacties](#dependency-actions) voor meer informatie.
>
>

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Afhankelijkheidsacties

Standaard wordt een afhankelijke taak of set taken alleen uitgevoerd nadat een bovenliggende taak is voltooid. In sommige scenario's u afhankelijke taken uitvoeren, zelfs als de bovenliggende taak mislukt. U het standaardgedrag overschrijven door een afhankelijkheidsactie op te geven. Een afhankelijkheidsactie geeft aan of een afhankelijke taak in aanmerking komt voor uitvoeren, op basis van het succes of falen van de bovenliggende taak. 

Stel dat een afhankelijke taak wacht op gegevens van de voltooiing van de upstreamtaak. Als de upstream-taak mislukt, kan de afhankelijke taak mogelijk nog steeds worden uitgevoerd met oudere gegevens. In dit geval kan een afhankelijkheidsactie opgeven dat de afhankelijke taak in aanmerking komt voor uitvoeren ondanks het mislukken van de bovenliggende taak.

Een afhankelijkheidsactie is gebaseerd op een exitvoorwaarde voor de bovenliggende taak. U een afhankelijkheidsactie opgeven voor een van de volgende exitvoorwaarden. zie voor .NET de klasse [ExitConditions][net_exitconditions] voor meer informatie:

- Wanneer een pre-processing fout optreedt.
- Wanneer er een fout optreedt bij het uploaden van bestanden. Als de taak wordt afgesloten met een exitcode die is opgegeven via **exitCodes** of **exitCodeRanges,** en vervolgens een fout voor het uploaden van bestanden tegenkomt, heeft de actie die is opgegeven door de exitcode voorrang.
- Wanneer de taak wordt afgesloten met een exitcode die is gedefinieerd door de eigenschap **ExitCodes.**
- Wanneer de taak wordt afgesloten met een exitcode die binnen een bereik valt dat is opgegeven door de eigenschap **ExitCodeRanges.**
- De standaardcase, als de taak wordt afgesloten met een exitcode die niet is gedefinieerd door **ExitCodes** of **ExitCodeRanges,** of als de taak wordt afgesloten met een pre-processing fout en de eigenschap **PreProcessingError** niet is ingesteld, of als de taak mislukt met een fout bij het uploaden van bestanden en de eigenschap **FileUploadError** niet is ingesteld. 

Als u een afhankelijkheidsactie wilt opgeven in .NET, stelt u de [ExitOptions][net_exitoptions]in . [DependencyAction][net_dependencyaction] eigenschap voor de exit voorwaarde. De eigenschap **DependencyAction** heeft een van de twee waarden:

- Als u de eigenschap **DependencyAction** instelt **op Voldoen,** geeft u aan dat afhankelijke taken in aanmerking komen om uit te voeren als de bovenliggende taak wordt afgesloten met een opgegeven fout.
- Als u de eigenschap **DependencyAction** instelt **op Blokkeren,** geeft u aan dat afhankelijke taken niet in aanmerking komen voor uitvoeren.

De standaardinstelling voor de eigenschap **DependencyAction** is **Voldoen** aan voor exitcode 0 en **Blokkeren** voor alle andere exitvoorwaarden.

In het volgende codefragment wordt de eigenschap **DependencyAction** ingesteld voor een bovenliggende taak. Als de bovenliggende taak wordt afgesloten met een voorbewerkingsfout of met de opgegeven foutcodes, wordt de afhankelijke taak geblokkeerd. Als de bovenliggende taak wordt afgesloten met een andere fout zonder nul, komt de afhankelijke taak in aanmerking voor uitvoeren.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Codevoorbeeld
Het voorbeeldproject [Taakafhankelijkheden][github_taskdependencies] is een van de [azure batchcodevoorbeelden][github_samples] op GitHub. Deze Visual Studio-oplossing toont:

- Taakafhankelijkheid van een taak inschakelen
- Taken maken die afhankelijk zijn van andere taken
- Deze taken uitvoeren op een pool van compute nodes.

## <a name="next-steps"></a>Volgende stappen
### <a name="application-deployment"></a>Toepassingsimplementatie
De [functie toepassingspakketten](batch-application-packages.md) van Batch biedt een eenvoudige manier om de toepassingen die uw taken uitvoeren op compute nodes te implementeren en te versionen.

### <a name="installing-applications-and-staging-data"></a>Toepassingen en faseringsgegevens installeren
Zie [Toepassingen installeren en gegevens met tijdelijke bestanden op batchcomputenodes][forum_post] plaatsen in het Azure Batch-forum voor een overzicht van methoden voor het voorbereiden van uw knooppunten om taken uit te voeren. Geschreven door een van de Azure Batch-teamleden, dit bericht is een goede inleiding over de verschillende manieren om toepassingen, taakinvoergegevens en andere bestanden naar uw compute nodes te kopiëren.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: één-op-één afhankelijkheid"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: één-op-veel afhankelijkheid"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: afhankelijkheid taak-id-bereik"
