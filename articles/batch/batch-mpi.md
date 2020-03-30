---
title: Multifunctionele taken gebruiken om MPI-toepassingen uit te voeren - Azure Batch | Microsoft Documenten
description: Meer informatie over het uitvoeren van MPI-toepassingen (Message Passing Interface) met behulp van het taaktype meerdere instance's in Azure Batch.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 03/13/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1896fea3c401299b4f77235ab3c02d85708b7041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023664"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Meerdere taken gebruiken om MPI-toepassingen (Message Passing Interface) uit te voeren in Batch

Met taken met meerdere instanties u een Azure Batch-taak tegelijk uitvoeren op meerdere compute-knooppunten. Deze taken maken high performance computing-scenario's mogelijk, zoals MPI-toepassingen (Message Passing Interface) in Batch. In dit artikel leert u hoe u taken met meerdere instantie uitvoert met de [Batch .NET-bibliotheek.][api_net]

> [!NOTE]
> Terwijl de voorbeelden in dit artikel zich richten op Batch .NET, MS-MPI en Windows compute nodes, zijn de hier besproken taakconcepten met meerdere instanties van toepassing op andere platforms en technologieën (Python en Intel MPI op Linux-knooppunten, bijvoorbeeld).
>
>

## <a name="multi-instance-task-overview"></a>Overzicht van taken met meerdere instance's
In Batch wordt elke taak normaal gesproken uitgevoerd op één compute-knooppunt: u verzendt meerdere taken naar een taak en de batchservice plant elke taak voor uitvoering op een knooppunt. Door de instellingen voor **meerdere instanties**van een taak te configureren, vertelt u Batch echter om in plaats daarvan één primaire taak en meerdere subtaken te maken die vervolgens op meerdere knooppunten worden uitgevoerd.

![Overzicht van taken met meerdere instance's][1]

Wanneer u een taak met instellingen voor meerdere instanties naar een taak verzendt, voert Batch verschillende stappen uit die uniek zijn voor taken met meerdere instanties:

1. Met de batchservice worden één **primaire** en meerdere **subtaken** gemaakt op basis van de instellingen voor meerdere taken. Het totale aantal taken (primair plus alle subtaken) komt overeen met het aantal **exemplaren** (compute nodes) dat u opgeeft in de instellingen voor meerdere instanties.
2. Batch wijst een van de compute nodes aan als de **master**en plant de primaire taak die moet worden uitgevoerd op de master. Het plant de subtaken uit te voeren op de rest van de compute nodes toegewezen aan de multi-instance taak, een subtaak per knooppunt.
3. De primaire en alle subtaken downloaden **alle algemene bronbestanden** die u opgeeft in de instellingen voor meerdere taken.
4. Nadat de algemene bronbestanden zijn gedownload, voeren de primaire en subtaken de **coördinatieopdracht** uit die u opgeeft in de instellingen voor meerdere taken. De coördinatieopdracht wordt meestal gebruikt om knooppunten voor te bereiden op het uitvoeren van de taak. Dit kan bestaan uit het starten van `smpd.exe`achtergrondservices (zoals die van [Microsoft MPI)][msmpi_msdn]en controleren of de knooppunten klaar zijn om internodeberichten te verwerken.
5. De primaire taak voert de **toepassingsopdracht** uit op het hoofdknooppunt *nadat* de coördinatieopdracht is voltooid door de primaire en alle subtaken. De opdracht toepassing is de opdrachtregel van de multi-instance taak zelf en wordt alleen uitgevoerd door de primaire taak. In een [op MS-MPI][msmpi_msdn]gebaseerde oplossing voert u uw TOEPASSING `mpiexec.exe`met MPI uit met behulp van .

> [!NOTE]
> Hoewel het functioneel onderscheidend is, is de "multi-instance taak" geen uniek taaktype zoals de [Taak StartTask][net_starttask] of [JobPreparationTask.][net_jobprep] De taak met meerdere instanties is gewoon een standaardbatchtaak[(CloudTask][net_task] in Batch .NET) waarvan de instellingen voor meerdere instanties zijn geconfigureerd. In dit artikel noemen we dit de **taak met meerdere gevallen.**
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Vereisten voor taken met meerdere taken
Voor taken met meerdere instanties is een pool nodig **met internodecommunicatie ingeschakeld**en met **gelijktijdige taakuitvoering uitgeschakeld**. Als u gelijktijdige taakuitvoering wilt uitschakelen, stelt u de eigenschap [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool) in op 1.

> [!NOTE]
> Batch [beperkt](batch-quota-limit.md#pool-size-limits) de grootte van een groep waarop communicatie tussen knooppunten is ingeschakeld.


In dit codefragment ziet u hoe u een groep maakt voor taken met meerdere instanties met behulp van de Batch .NET-bibliotheek.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> Als u een taak met meerdere instanties probeert uit te voeren in een groep met internodecommunicatie uitgeschakeld of met een *maxTasksPerNode-waarde* groter dan 1, wordt de taak nooit gepland- deze blijft voor onbepaalde tijd in de status 'actief'. 


### <a name="use-a-starttask-to-install-mpi"></a>Een StartTask gebruiken om MPI te installeren
Als u MPI-toepassingen met een taak met meerdere instanties wilt uitvoeren, moet u eerst een MPI-implementatie (bijvoorbeeld MS-MPI of Intel MPI) installeren op de compute-knooppunten in de groep. Dit is een goed moment om een [StartTask][net_starttask]te gebruiken, die wordt uitgevoerd wanneer een knooppunt lid wordt van een groep of opnieuw wordt gestart. Met dit codefragment wordt een StartTask gemaakt die het MS-MPI-installatiepakket opgeeft als een [bronbestand][net_resourcefile]. De opdrachtregel van de starttaak wordt uitgevoerd nadat het bronbestand is gedownload naar het knooppunt. In dit geval voert de opdrachtregel een onbewaakte installatie van MS-MPI uit.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Externe directe geheugentoegang (RDMA)
Wanneer u een [RDMA-formaat](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kiest, zoals A9 voor de compute nodes in uw Batch-groep, kan uw MPI-toepassing profiteren van het krachtige, low-latency remote direct memory access (RDMA)-netwerk van Azure.

Zoek naar de maten die in de volgende artikelen zijn opgegeven als "RDMA geschikt":

* **Groepen CloudServiceConfiguration**

  * [Formaten voor Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (alleen Windows)
* **VirtualMachineConfiguration-groepen**

  * [Formaten voor virtuele machines in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Groottes voor virtuele machines in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Om te profiteren van RDMA op [Linux compute nodes,](batch-linux-nodes.md)moet u **Intel MPI** gebruiken op de knooppunten. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Een taak met meerdere instanties maken met Batch .NET
Nu we de vereisten voor het zwembad en de mpi-pakketinstallatie hebben gedekt, maken we de taak voor meerdere voorbeelden. In dit fragment maken we een standaard [CloudTask][net_task]en configureren we de eigenschap [MultiInstanceSettings.][net_multiinstance_prop] Zoals eerder vermeld, is de taak met meerdere instanties geen afzonderlijk taaktype, maar een standaardbatchtaak die is geconfigureerd met instellingen voor meerdere instanties.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Primaire taak en subtaken
Wanneer u de instellingen voor meerdere instanties voor een taak maakt, geeft u het aantal compute nodes op dat de taak moet uitvoeren. Wanneer u de taak naar een taak verzendt, maakt de batchservice één **primaire** taak en voldoende **subtaken** die samen overeenkomen met het aantal knooppunten dat u hebt opgegeven.

Deze taken krijgen een geheel getal-id toegewezen in het bereik van 0 tot *getalOfInstances* - 1. De taak met id 0 is de primaire taak en alle andere id's zijn subtaken. Als u bijvoorbeeld de volgende instellingen voor meerdere instanties voor een taak maakt, heeft de primaire taak een id van 0 en hebben de subtaken id's van 1 tot en met 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Masterknooppunt
Wanneer u een taak met meerdere instanties indient, wijst de batchservice een van de compute-knooppunten aan als het hoofdknooppunt en plant de primaire taak die moet worden uitgevoerd op het hoofdknooppunt. De subtaken worden gepland om uit te voeren op de rest van de knooppunten toegewezen aan de multi-instance taak.

## <a name="coordination-command"></a>Coördinatie, opdracht
De **coördinatieopdracht** wordt uitgevoerd door zowel de primaire als de subtaken.

De aanroep van de coördinatieopdracht is blokkeren-- Batch voert de toepassingsopdracht pas uit nadat de coördinatieopdracht is teruggekeerd voor alle subtaken. De coördinatieopdracht moet daarom alle vereiste achtergrondservices starten, controleren of ze klaar zijn voor gebruik en vervolgens worden afgesloten. Deze coördinatieopdracht voor een oplossing met MS-MPI-versie 7 start bijvoorbeeld de SMPD-service op het knooppunt en wordt vervolgens afgesloten:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Let op `start` het gebruik van in deze coördinatie opdracht. Dit is vereist `smpd.exe` omdat de toepassing niet onmiddellijk na de uitvoering terugkeert. Zonder het gebruik van de [startopdracht][cmd_start] zou deze coördinatieopdracht niet terugkeren en daarom de toepassingsopdracht blokkeren om uit te voeren.

## <a name="application-command"></a>Toepassing, opdracht
Zodra de primaire taak en alle subtaken klaar zijn met het uitvoeren van de coördinatieopdracht, wordt de opdrachtregel van de opdrachttaak met meerdere instance's *alleen*uitgevoerd door de primaire taak. We noemen dit de **toepassingsopdracht** om het te onderscheiden van de coördinatieopdracht.

Voor MS-MPI-toepassingen gebruikt u de toepassingsopdracht om uw `mpiexec.exe`toepassing met MPI uit te voeren met . Hier is bijvoorbeeld een toepassingsopdracht voor een oplossing met MS-MPI-versie 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Omdat MS-MPI's `mpiexec.exe` `CCP_NODES` de variabele standaard gebruiken (zie [Omgevingsvariabelen)](#environment-variables)sluit de bovenstaande opdrachtregel van de voorbeeldtoepassing deze uit.
>
>

## <a name="environment-variables"></a>Omgevingsvariabelen
Batch maakt verschillende [omgevingsvariabelen][msdn_env_var] die specifiek zijn voor taken met meerdere instanties op de compute nodes die zijn toegewezen aan een taak met meerdere instanties. Uw coördinatie- en toepassingsopdrachtregels kunnen verwijzen naar deze omgevingsvariabelen, evenals de scripts en programma's die ze uitvoeren.

De volgende omgevingsvariabelen worden gemaakt door de batchservice voor gebruik door taken met meerdere taken:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Zie [Compute node-omgevingsvariabelen][msdn_env_var]voor meer informatie over deze en de andere batch-omgevingsvariabelen voor compute node, inclusief hun inhoud en zichtbaarheid.

> [!TIP]
> Het batch-Linux MPI-codevoorbeeld bevat een voorbeeld van hoe verschillende van deze omgevingsvariabelen kunnen worden gebruikt. Het [coördinatie-cmd Bash-script][coord_cmd_example] downloadt algemene toepassings- en invoerbestanden uit Azure Storage, maakt een NFS-share (Network File System) op het hoofdknooppunt mogelijk en configureert de andere knooppunten die aan de taak met meerdere instanties zijn toegewezen als NFS-clients.
>
>

## <a name="resource-files"></a>Bronbestanden
Er zijn twee sets resourcebestanden die u moet overwegen voor taken met meerdere taken: **algemene bronbestanden** die *alle* taken downloaden (zowel primaire als subtaken) en de **bronbestanden** die zijn opgegeven voor de taak met meerdere taken zelf, die *alleen de primaire* taak downloadt.

U een of meer **algemene bronbestanden** opgeven in de instellingen voor meerdere instanties voor een taak. Deze algemene bronbestanden worden gedownload van [Azure Storage](../storage/common/storage-introduction.md) naar de **gedeelde map** taak van elk knooppunt door de primaire en alle subtaken. U hebt toegang tot de gedeelde taakmap via `AZ_BATCH_TASK_SHARED_DIR` opdrachtregels voor toepassingen en coördinatie met behulp van de omgevingsvariabele. Het `AZ_BATCH_TASK_SHARED_DIR` pad is identiek op elk knooppunt dat is toegewezen aan de taak met meerdere instanties, zodat u één coördinatieopdracht delen tussen de primaire en alle subtaken. Batch deelt de directory niet in een externe toegangszin, maar u deze gebruiken als een mount- of share-punt zoals eerder vermeld in de tip over omgevingsvariabelen.

Bronbestanden die u opgeeft voor de taak met meerdere instance's `AZ_BATCH_TASK_WORKING_DIR`zelf, worden standaard gedownload naar de werkmap van de taak. Zoals vermeld, in tegenstelling tot gemeenschappelijke bronbestanden, worden alleen de primaire taakbronbestanden gedownload die zijn opgegeven voor de taak met meerdere taken zelf.

> [!IMPORTANT]
> Gebruik altijd de `AZ_BATCH_TASK_SHARED_DIR` omgevingsvariabelen en `AZ_BATCH_TASK_WORKING_DIR` verwijs naar deze mappen in uw opdrachtregels. Probeer de paden niet handmatig te construeren.
>
>

## <a name="task-lifetime"></a>Levensduur van de taak
De levensduur van de primaire taak bepaalt de levensduur van de volledige taak met meerdere taken. Wanneer de primaire uitgangen worden afgesloten, worden alle subtaken beëindigd. De exitcode van het primaire is de exitcode van de taak en wordt daarom gebruikt om het succes of falen van de taak te bepalen voor nieuwe doeleinden.

Als een van de subtaken mislukt, mislukt u bijvoorbeeld met een niet-nulretourcode, mislukt de hele taak met meerdere instanties. De taak met meerdere instancewordt vervolgens beëindigd en opnieuw geprobeerd, tot de limiet voor opnieuw proberen.

Wanneer u een taak met meerdere instanties verwijdert, worden de primaire en alle subtaken ook verwijderd door de batchservice. Alle subtaakmappen en hun bestanden worden verwijderd uit de compute nodes, net als voor een standaardtaak.

[Taakbeperkingen][net_taskconstraints] voor een taak met meerdere instanties, zoals de eigenschappen [MaxTaskRetryCount,][net_taskconstraint_maxretry] [MaxWallClockTime][net_taskconstraint_maxwallclock]en [RetentionTime,][net_taskconstraint_retention] worden gehonoreerd zoals ze zijn voor een standaardtaak en zijn van toepassing op de primaire en alle subtaken. Als u echter de eigenschap [RetentionTime][net_taskconstraint_retention] wijzigt nadat u de taak met meerdere instantie aan de taak hebt toegevoegd, wordt deze wijziging alleen toegepast op de primaire taak. Alle subtaken blijven de oorspronkelijke [retentiontime][net_taskconstraint_retention]gebruiken.

De recente takenlijst van een compute node geeft de id van een subtaak weer als de recente taak deel uitmaakte van een taak met meerdere instanties.

## <a name="obtain-information-about-subtasks"></a>Informatie verkrijgen over subtaken
Als u informatie wilt verkrijgen over subtaken met behulp van de Batch .NET-bibliotheek, roept u de methode [CloudTask.ListSubtasks aan.][net_task_listsubtasks] Met deze methode worden informatie over alle subtaken geretourneerd en informatie over het rekenknooppunt waarmee de taken zijn uitgevoerd. Op basis van deze informatie u de hoofdmap van elke subtaak, de pool-id, de huidige status, de exitcode en meer bepalen. U deze informatie gebruiken in combinatie met de [methode PoolOperations.GetNodeFile][poolops_getnodefile] om de bestanden van de subtaak te verkrijgen. Houd er rekening mee dat deze methode geen informatie voor de primaire taak (id 0) retourneert.

> [!NOTE]
> Tenzij anders vermeld, zijn Batch .NET-methoden die werken op de [cloudtaak][net_task] met meerdere instantie zelf *alleen* van toepassing op de primaire taak. Wanneer u bijvoorbeeld de methode [CloudTask.ListNodeFiles][net_task_listnodefiles] aanroept voor een taak met meerdere instanties, worden alleen de bestanden van de primaire taak geretourneerd.
>
>

In het volgende codefragment ziet u hoe u subtaakgegevens verkrijgen en hoe u de inhoud van het bestand aanvragen van de knooppunten waarop ze zijn uitgevoerd.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Codevoorbeeld
Het voorbeeld [van de code multiinstancetasks][github_mpi] op GitHub laat zien hoe u een taak met meerdere instanties gebruiken om een [MS-MPI-toepassing][msmpi_msdn] uit te voeren op batchcomputenodes. Volg de stappen in [Voorbereiding](#preparation) en [uitvoering](#execution) om het voorbeeld uit te voeren.

### <a name="preparation"></a>Voorbereiding
1. Volg de eerste twee stappen in [Hoe een eenvoudig MS-MPI-programma te compileren en uit te voeren.][msmpi_howto] Dit voldoet aan de voorwaarden voor de volgende stap.
2. Bouw een *releaseversie* van het [MPIHelloWorld-voorbeeld][helloworld_proj] MPI-programma. Dit is het programma dat wordt uitgevoerd op compute nodes door de multi-instance taak.
3. Maak een zip-bestand met `MPIHelloWorld.exe` (dat u `MSMpiSetup.exe` hebt gebouwd stap 2) en (dat u hebt gedownload stap 1). Je uploadt dit zip-bestand als een aanvraagpakket in de volgende stap.
4. Gebruik de [Azure-portal][portal] om een [batchtoepassing](batch-application-packages.md) met de naam 'MPIHelloWorld' te maken en het zip-bestand op te geven dat u in de vorige stap hebt gemaakt als versie "1.0" van het toepassingspakket. Zie [Toepassingen uploaden en beheren](batch-application-packages.md#upload-and-manage-applications) voor meer informatie.

> [!TIP]
> Bouw een *Release-versie* van, `MPIHelloWorld.exe` zodat u geen extra afhankelijkheden `msvcp140d.dll` hoeft `vcruntime140d.dll`op te nemen (bijvoorbeeld of ) in uw toepassingspakket.
>
>

### <a name="execution"></a>Uitvoering
1. Download de [azure-batch-samples][github_samples_zip] van GitHub.
2. Open de MultiInstanceTasks-oplossing in Visual Studio 2019. **solution** Het `MultiInstanceTasks.sln` oplossingsbestand bevindt zich in:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Voer uw batch- en `AccountSettings.settings` opslagaccountreferenties in in het project **Microsoft.Azure.Batch.Samples.Common.**
4. **Bouw en voer** de multiinstancetasks-oplossing uit om de MPI-voorbeeldtoepassing uit te voeren op compute nodes in een batchgroep.
5. *Optioneel:* gebruik de [Azure-portal][portal] of [Batch Explorer][batch_labs] om de voorbeeldgroep, taak en taak ('MultiInstanceSamplePool', 'MultiInstanceSampleJob', 'MultiInstanceSampleTask') te onderzoeken voordat u de resources verwijdert.

> [!TIP]
> Je [Visual Studio Community][visual_studio] gratis downloaden als je geen Visual Studio hebt.
>
>

De `MultiInstanceTasks.exe` uitvoer van is vergelijkbaar met de volgende:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Volgende stappen
* De Blog Microsoft HPC & Azure Batch Team bespreekt [MPI-ondersteuning voor Linux op Azure Batch][blog_mpi_linux]en bevat informatie over het gebruik van [OpenFOAM][openfoam] met Batch. U Python-codevoorbeelden vinden voor het [Voorbeeld Van OpenFOAM op GitHub.][github_mpi]
* Meer informatie over het [maken van groepen Linux-compute nodes](batch-linux-nodes.md) voor gebruik in uw Azure Batch MPI-oplossingen.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: https://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: https://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Overzicht van meerdere gevallen"
