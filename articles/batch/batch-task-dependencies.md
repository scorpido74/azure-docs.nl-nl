---
title: Taak afhankelijkheden maken om taken uit te voeren
description: Maak taken die afhankelijk zijn van de voltooiing van andere taken voor het verwerken van MapReduce stijl en soort gelijke big data werk belastingen in Azure Batch.
ms.topic: how-to
ms.date: 05/22/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4aad67b4537befd251798aac7601bc4efcc276f2
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965226"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Taak afhankelijkheden maken om taken uit te voeren die afhankelijk zijn van andere taken

U kunt taak afhankelijkheden definiëren om een taak of een set taken alleen uit te voeren nadat een bovenliggende taak is voltooid. Enkele scenario's waarbij taak afhankelijkheden handig zijn, zijn onder andere:

- MapReduce-werk belastingen in de Cloud.
- Taken waarvan de taken voor gegevens verwerking kunnen worden uitgedrukt als een Directed Acyclic Graph (DAG).
- Processen vóór rendering en na het renderen, waarbij elke taak moet worden voltooid voordat de volgende taak kan worden gestart.
- Een andere taak waarin downstream-taken afhankelijk zijn van de uitvoer van upstream-taken.

Met batch taak afhankelijkheden kunt u taken maken die zijn gepland voor uitvoering op reken knooppunten na het volt ooien van een of meer bovenliggende taken. U kunt bijvoorbeeld een taak maken waarmee elk frame van een 3D-film met afzonderlijke, parallelle taken wordt weer gegeven. De laatste taak--de taak samen voegen: Hiermee worden de gerenderde frames pas samengevoegd in de volledige film nadat alle frames zijn gerenderd.

Standaard worden afhankelijke taken alleen gepland voor uitvoering nadat de bovenliggende taak is voltooid. U kunt een afhankelijkheids actie opgeven om het standaard gedrag te overschrijven en taken uit te voeren wanneer de bovenliggende taak mislukt. Zie de sectie [afhankelijkheids acties](#dependency-actions) voor meer informatie.  

U kunt taken maken die afhankelijk zijn van andere taken in een een-op-een-of een-op-veel-relatie. U kunt ook een bereik afhankelijkheid maken waarbij een taak afhankelijk is van de voltooiing van een groep taken binnen een opgegeven reeks taak-Id's. U kunt deze drie basis scenario's combi neren om veel-op-veel-relaties te maken.

## <a name="task-dependencies-with-batch-net"></a>Taak afhankelijkheden met batch .NET

In dit artikel wordt beschreven hoe taak afhankelijkheden worden geconfigureerd met behulp van de [batch .net][net_msdn] -bibliotheek. We laten u eerst zien hoe u de [taak afhankelijkheid kunt inschakelen](#enable-task-dependencies) voor uw taken en hoe u [een taak configureert met afhankelijkheden](#create-dependent-tasks). Daarnaast wordt beschreven hoe u een afhankelijkheids actie opgeeft voor het uitvoeren van afhankelijke taken als het bovenliggende item mislukt. Ten slotte bespreken we de [afhankelijkheids scenario's](#dependency-scenarios) die door batch worden ondersteund.

## <a name="enable-task-dependencies"></a>Taak afhankelijkheden inschakelen

Als u taak afhankelijkheden in uw batch-toepassing wilt gebruiken, moet u eerst de taak configureren voor het gebruik van taak afhankelijkheden. Schakel in batch .NET het in op uw [eigenschap cloudjob][net_cloudjob] door de eigenschap [UsesTaskDependencies][net_usestaskdependencies] in te stellen op `true` :

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

In het voor gaande code fragment is ' batchClient ' een instantie van de klasse [batchClient][net_batchclient] .

## <a name="create-dependent-tasks"></a>Afhankelijke taken maken

Als u een taak wilt maken die afhankelijk is van het volt ooien van een of meer bovenliggende taken, kunt u opgeven dat de taak afhankelijk is van de andere taken. Configureer de [CloudTask][net_cloudtask]in batch .net. De eigenschap [DependsOn][net_dependson] met een exemplaar van de klasse [TaskDependencies][net_taskdependencies] :

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Met dit code fragment maakt u een afhankelijke taak met taak-ID "bloemen". De taak "bloemen" is afhankelijk van de taken "regen" en "Sun". Taak "bloemen" wordt alleen gepland om te worden uitgevoerd op een reken knooppunt nadat taken "regen" en "Sun" zijn voltooid.

> [!NOTE]
> Standaard wordt een taak als voltooid beschouwd als deze de status **voltooid** heeft en de **afsluit code** ervan `0` . In batch .NET betekent dit een [CloudTask][net_cloudtask]. Waarde van de eigenschap [State][net_taskstate] van `Completed` en de [TaskExecutionInformation][net_taskexecutioninformation]van het CloudTask.[ ][net_exitcode]De waarde van de eigenschap ExitCode is `0` . Zie de sectie [afhankelijkheids acties](#dependency-actions) voor meer informatie over hoe u dit kunt wijzigen.

## <a name="dependency-scenarios"></a>Afhankelijkheids scenario's

Er zijn drie elementaire scenario's voor taak afhankelijkheden die u in Azure Batch kunt gebruiken: een-op-een-, een-op-veel-en taak-ID-bereik afhankelijkheid. Deze kunnen worden gecombineerd om een vierde scenario te bieden. veel-op-veel.

| Omstandigheden&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Voorbeeld | Zien |
|:---:| --- | --- |
|  [Een-op-een](#one-to-one) |*taakb kan pas* is afhankelijk van *taaka* <p/> *taakb kan pas* wordt niet gepland voor uitvoering totdat *taaka* is voltooid |![Diagram: een-op-een-taak afhankelijkheid][1] |
|  [Een-op-veel](#one-to-many) |*taakc* is afhankelijk van zowel *Taaka* als *taakb kan pas* <p/> *taakc* wordt pas ingepland als de uitvoering van *Takena* en *taakb kan pas* is voltooid |![Diagram: een-op-veel-taak afhankelijkheid][2] |
|  [Bereik taak-ID](#task-id-range) |*taken* zijn afhankelijk van een reeks taken <p/> de *taak* wordt niet gepland voor uitvoering totdat de taken met de id *1* t/m *10* zijn voltooid |![Diagram: afhankelijkheid van taak-id-bereik][3] |

> [!TIP]
> U kunt **veel-op-veel** -relaties maken, zoals de taken C, D, E en F elk zijn afhankelijk van de taken A en B. Dit is bijvoorbeeld handig in scenario's met een geparalleld preproces waarbij uw downstream-taken afhankelijk zijn van de uitvoer van meerdere upstream-taken.
> 
> In de voor beelden in deze sectie wordt een afhankelijke taak alleen uitgevoerd nadat de bovenliggende taken zijn voltooid. Dit gedrag is het standaard gedrag voor een afhankelijke taak. U kunt een afhankelijke taak uitvoeren nadat een bovenliggende taak is mislukt door een afhankelijkheids actie op te geven om het standaard gedrag te negeren. Zie de sectie [afhankelijkheids acties](#dependency-actions) voor meer informatie.

### <a name="one-to-one"></a>Een-op-een

Bij een een-op-een-relatie is een taak afhankelijk van de geslaagde voltooiing van één bovenliggende taak. Als u de afhankelijkheid wilt maken, geeft u één taak-ID op voor de [TaskDependencies][net_taskdependencies]. [OnId][net_onid] static-methode wanneer u de eigenschap [DependsOn][net_dependson] van [CloudTask][net_cloudtask]invult.

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

Bij een een-op-veel-relatie is een taak afhankelijk van het volt ooien van meerdere bovenliggende taken. Als u de afhankelijkheid wilt maken, geeft u een verzameling taak-Id's op voor de [TaskDependencies][net_taskdependencies]. [OnIds][net_onids] static-methode wanneer u de eigenschap [DependsOn][net_dependson] van [CloudTask][net_cloudtask]invult.

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

### <a name="task-id-range"></a>Bereik taak-ID

In een afhankelijkheid van een reeks bovenliggende taken is een taak afhankelijk van de voltooiing van taken waarvan de Id's binnen een bereik liggen.
Als u de afhankelijkheid wilt maken, geeft u de eerste en laatste taak-Id's op in het bereik tot de [TaskDependencies][net_taskdependencies]. [OnIdRange][net_onidrange] static-methode wanneer u de eigenschap [DependsOn][net_dependson] van [CloudTask][net_cloudtask]invult.

> [!IMPORTANT]
> Wanneer u taak-ID-bereiken voor uw afhankelijkheden gebruikt, worden alleen de taken met Id's die gehele waarden vertegenwoordigen, geselecteerd door het bereik. Daarom worden in het bereik `1..10` taken `3` en `7` , maar niet geselecteerd `5flamingoes` . 
>
> Voorloop nullen zijn niet significant bij het evalueren van de bereik afhankelijkheden, dus taken met teken reeks `4` -id's, `04` en `004` zijn allemaal *binnen* het bereik en ze worden allemaal beschouwd als taak `4` , zodat de eerste die moet worden voltooid, aan de afhankelijkheid wordt voldaan.
>
> Elke taak in het bereik moet voldoen aan de afhankelijkheid, hetzij door het volt ooien of door het volt ooien van een fout die is toegewezen aan een afhankelijkheids actie ingesteld op **voldaan**. Zie de sectie [afhankelijkheids acties](#dependency-actions) voor meer informatie.

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

## <a name="dependency-actions"></a>Afhankelijkheids acties

Een afhankelijke taak of set taken wordt standaard pas uitgevoerd nadat een bovenliggende taak is voltooid. In sommige gevallen wilt u wellicht afhankelijke taken uitvoeren, zelfs als de bovenliggende taak mislukt. U kunt het standaard gedrag negeren door een afhankelijkheids actie op te geven. Een afhankelijkheids actie geeft aan of een afhankelijke taak in aanmerking komt voor uitvoering, op basis van het slagen of mislukken van de bovenliggende taak. 

Stel bijvoorbeeld dat een afhankelijke taak wacht op gegevens van het volt ooien van de upstream-taak. Als de upstream-taak mislukt, kan de afhankelijke taak mogelijk nog steeds worden uitgevoerd met oudere gegevens. In dit geval kan een afhankelijkheids actie opgeven dat de afhankelijke taak kan worden uitgevoerd ondanks het mislukken van de bovenliggende taak.

Een afhankelijkheids actie is gebaseerd op een afsluit voorwaarde voor de bovenliggende taak. U kunt een afhankelijkheids actie voor elk van de volgende afsluit voorwaarden opgeven. voor .NET raadpleegt u de klasse [ExitConditions][net_exitconditions] voor meer informatie:

- Als er een fout optreedt die voorafgaat aan de verwerking.
- Wanneer er een fout optreedt bij het uploaden van het bestand. Als de taak wordt afgesloten met een afsluit code die is opgegeven via **exitCodes** of **exitCodeRanges**, en vervolgens een fout bij het uploaden van het bestand ondervindt, heeft de actie die is opgegeven door de afsluit code prioriteit.
- Wanneer de taak wordt afgesloten met een afsluit code die is gedefinieerd door de eigenschap **ExitCodes** .
- Wanneer de taak wordt afgesloten met een afsluit code die binnen een bereik valt dat is opgegeven met de eigenschap **ExitCodeRanges** .
- Als de taak wordt afgesloten met een afsluit code die niet is gedefinieerd door **ExitCodes** of **ExitCodeRanges**, of als de taak wordt afgesloten met een pre-verwerkings fout en de eigenschap **PreProcessingError** niet is ingesteld, of als de taak mislukt met een fout bij het uploaden van het bestand en de eigenschap **FileUploadError** niet is ingesteld. 

Als u een afhankelijkheids actie in .NET wilt opgeven, stelt u de [ExitOptions][net_exitoptions]in. [DependencyAction][net_dependencyaction] -eigenschap voor de afsluit voorwaarde. De eigenschap **DependencyAction** neemt een van de twee volgende waarden:

- Als u de eigenschap **DependencyAction** instelt op **voldaan** , geeft u aan dat afhankelijke taken kunnen worden uitgevoerd als de bovenliggende taak wordt afgesloten met een opgegeven fout.
- Als u de eigenschap **DependencyAction** instelt op **blok keren** , wordt aangegeven dat er geen afhankelijke taken mogen worden uitgevoerd.

De standaard instelling voor de eigenschap **DependencyAction** is te **voldoen aan** de afsluit code 0 en **blok keren** voor alle andere afsluit voorwaarden.

Met het volgende code fragment wordt de eigenschap **DependencyAction** ingesteld voor een bovenliggende taak. Als de bovenliggende taak wordt afgesloten met een pre-verwerkings fout of met de opgegeven fout codes, wordt de afhankelijke taak geblokkeerd. Als de bovenliggende taak wordt afgesloten met een andere fout die niet gelijk is aan nul, komt de afhankelijke taak in aanmerking voor uitvoering.

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

Het [TaskDependencies][github_taskdependencies] -voorbeeld project is een van de [Azure batch code voorbeelden][github_samples] op github. Deze Visual Studio-oplossing demonstreert:

- Taak afhankelijkheid voor een taak inschakelen
- Taken maken die afhankelijk zijn van andere taken
- Taken uitvoeren op een pool van reken knooppunten.

## <a name="next-steps"></a>Volgende stappen

- De functie [toepassings pakketten](batch-application-packages.md) van batch biedt een eenvoudige manier om de toepassingen te implementeren en te installeren die uw taken uitvoeren op reken knooppunten.
- Zie [toepassingen en faserings gegevens installeren op batch Compute-knoop punten][forum_post] in het Azure batch-forum voor een overzicht van de methoden voor het voorbereiden van het uitvoeren van taken. Dit bericht is geschreven door een van de Azure Batch-team leden en is een goede primer op de verschillende manieren om toepassingen, invoer gegevens van taken en andere bestanden naar uw reken knooppunten te kopiëren.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_dependson]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_exitcode]: /dotnet/api/microsoft.azure.batch.taskexecutioninformation
[net_exitconditions]: /dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: /dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: /dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: /dotnet/api/microsoft.azure.batch
[net_onid]: /dotnet/api/microsoft.azure.batch.taskdependencies
[net_onids]: /dotnet/api/microsoft.azure.batch.taskdependencies
[net_onidrange]: /dotnet/api/microsoft.azure.batch.taskdependencies
[net_taskexecutioninformation]: /dotnet/api/microsoft.azure.batch.taskexecutioninformation
[net_taskstate]: /dotnet/api/microsoft.azure.batch.common.taskstate
[net_usestaskdependencies]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_taskdependencies]: /dotnet/api/microsoft.azure.batch.taskdependencies

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: een-op-een-afhankelijkheid"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: een-op-veel-afhankelijkheid"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: afhankelijkheid van taak-id-bereik"
