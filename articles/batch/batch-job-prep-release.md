---
title: Taken maken om & volledige taken op compute nodes voor te bereiden - Azure Batch
description: Gebruik voorbereidingstaken op taakniveau om gegevensoverdracht naar gegevensknooppunten van Azure Batch te minimaliseren en taken vrij te geven voor het opschonen van knooppunten bij het voltooien van de taak.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: d9f6f015c210592d5d8053b1b34d5357bb357629
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586781"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Taken voor taakvoorbereiding en taakrelease uitvoeren op batchcomputeknooppunten

 Een Azure Batch-taak vereist vaak een of andere vorm van installatie voordat de taken worden uitgevoerd en onderhoud na de taak wanneer de taken zijn voltooid. Mogelijk moet u algemene taakinvoergegevens downloaden naar uw compute nodes of taakuitvoergegevens uploaden naar Azure Storage nadat de taak is voltooid. U **taakvoorbereidings-** en **taakreleasetaken** gebruiken om deze bewerkingen uit te voeren.

## <a name="what-are-job-preparation-and-release-tasks"></a>Wat zijn taakvoorbereidings- en releasetaken?
Voordat de taken van een taak worden uitgevoerd, wordt de taak voor het voorbereiden van taken uitgevoerd op alle compute-knooppunten die zijn gepland om ten minste één taak uit te voeren. Zodra de taak is voltooid, wordt de taak voor het vrijgeven van taken uitgevoerd op elk knooppunt in de groep die ten minste één taak heeft uitgevoerd. Net als bij normale batchtaken u een opdrachtregel opgeven die moet worden aangeroepen wanneer een taakvoorbereiding of releasetaak wordt uitgevoerd.

Taakvoorbereidings- en releasetaken bieden bekende batchtaakfuncties zoals bestandsdownload[(bronbestanden),][net_job_prep_resourcefiles]verhoogde uitvoering, aangepaste omgevingsvariabelen, maximale uitvoeringsduur, aantal opnieuw proberen en bestandsbewaartijd.

In de volgende secties leert u hoe u de klassen [JobPreparationTask][net_job_prep] en [JobReleaseTask][net_job_release] gebruiken in de [Batch .NET-bibliotheek.][api_net]

> [!TIP]
> Taakvoorbereidings- en releasetaken zijn vooral handig in 'shared pool'-omgevingen, waarin een pool van compute-knooppunten blijft bestaan tussen taakuitvoeringen en door veel taken wordt gebruikt.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Wanneer taken voor het voorbereiden en vrijgeven van taken gebruiken
Job voorbereiding en job release taken zijn een goede pasvorm voor de volgende situaties:

**Algemene taakgegevens downloaden**

Batchtaken vereisen vaak een gemeenschappelijke set gegevens als invoer voor de taken van de taak. In dagelijkse risicoanalyseberekeningen zijn marktgegevens bijvoorbeeld taakspecifiek, maar gemeenschappelijk voor alle taken in de taak. Deze marktgegevens, vaak meerdere gigabytes groot, mogen slechts één keer naar elk compute-knooppunt worden gedownload, zodat elke taak die op het knooppunt wordt uitgevoerd, deze kan gebruiken. Gebruik een **taakvoorbereidingstaak** om deze gegevens naar elk knooppunt te downloaden voordat de andere taken van de taak worden uitgevoerd.

**Taak- en taakuitvoer verwijderen**

In een 'gedeelde groep'-omgeving, waarin de compute nodes van een groep niet buiten bedrijf worden gesteld tussen taken, moet u mogelijk taakgegevens tussen uitvoeringen verwijderen. Mogelijk moet u schijfruimte op de knooppunten besparen of voldoen aan het beveiligingsbeleid van uw organisatie. Gebruik een **taak voor taakrelease** om gegevens te verwijderen die zijn gedownload door een taakvoorbereidingstaak of die zijn gegenereerd tijdens het uitvoeren van taken.

**Logboekbehoud**

Misschien wilt u een kopie van logboekbestanden bewaren die uw taken genereren, of misschien crashdumpbestanden die kunnen worden gegenereerd door mislukte toepassingen. Gebruik in dergelijke gevallen een taak voor **het vrijgeven van** taken om deze gegevens te comprimeren en te uploaden naar een Azure [Storage-account.][azure_storage]

> [!TIP]
> Een andere manier om logboeken en andere taak- en taakuitvoergegevens te blijven bestaan, is door de bibliotheek [Azure Batch File Conventions](batch-task-output.md) te gebruiken.
>
>

## <a name="job-preparation-task"></a>Taak voor het voorbereiden van werk


Voordat de taken van een taak worden uitgevoerd, voert Batch de taakvoorhet voorbereiden van taken uit op elk compute-knooppunt dat is gepland om een taak uit te voeren. Batch wacht standaard tot de taakvoorbereidingstaak is voltooid voordat de taken worden uitgevoerd die op het knooppunt zijn uitgevoerd. U de service echter configureren om niet te wachten. Als het knooppunt opnieuw wordt gestart, wordt de taak voor het voorbereiden van de taak opnieuw uitgevoerd. U dit gedrag ook uitschakelen. Als u een taak hebt met een taakvoorbereidingstaak en een taaktaak voor taakbeheer, wordt de taak voor het voorbereiden van taken uitgevoerd vóór de taakbeheertaak, net als voor alle andere taken. De taakvoorbereidingstaak loopt altijd eerst.

De taakvoorbereiding wordt alleen uitgevoerd op knooppunten die zijn gepland om een taak uit te voeren. Dit voorkomt dat een voorbereidingstaak onnodig wordt uitgevoerd als een knooppunt geen taak wordt toegewezen. Dit kan gebeuren wanneer het aantal taken voor een taak kleiner is dan het aantal knooppunten in een groep. Dit is ook van toepassing wanneer [gelijktijdige taakuitvoering](batch-parallel-node-tasks.md) is ingeschakeld, waardoor sommige knooppunten niet actief zijn als het aantal taken lager is dan het totale aantal mogelijke gelijktijdige taken. Door de taak voor het voorbereiden van taken niet uit te voeren op niet-actieve knooppunten, u minder geld uitgeven aan kosten voor gegevensoverdracht.

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] verschilt van [CloudPool.StartTask][pool_starttask] in die JobPreparationTask wordt uitgevoerd aan het begin van elke taak, terwijl StartTask alleen wordt uitgevoerd wanneer een compute node voor het eerst deelneemt aan een groep of opnieuw wordt opgestart.
>


>## <a name="job-release-task"></a>Taak taak taak voor het vrijgeven van taken

Zodra een taak is gemarkeerd als voltooid, wordt de taak voor het vrijgeven van taken uitgevoerd op elk knooppunt in de groep die ten minste één taak heeft uitgevoerd. U markeert een taak als voltooid door een beëindigingsaanvraag uit te geven. De batchservice stelt vervolgens de taakstatus in op *beëindiging,* beëindigt alle actieve of actieve taken die aan de taak zijn gekoppeld en voert de taak voor het vrijgeven van taken uit. De taak wordt vervolgens verplaatst naar de *voltooide* status.

> [!NOTE]
> Taakverwijdering voert ook de taak voor het vrijgeven van taken uit. Als een taak echter al is beëindigd, wordt de releasetaak geen tweede keer uitgevoerd als de taak later wordt verwijderd.

Taken voor het vrijgeven van taken kunnen maximaal 15 minuten worden uitgevoerd voordat ze worden beëindigd door de batchservice. Zie de [verwijzingsdocumentatie van](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)de REST API voor meer informatie .
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Taken voor voorbereiden en vrijgeven van taken met Batch .NET
Als u een taakvoorbereidingstaak wilt gebruiken, wijst u een [object JobPreparationTask][net_job_prep] toe aan de eigenschap [CloudJob.JobPreparationTask van][net_job_prep_cloudjob] uw taak. Initiaal een [JobReleaseTask][net_job_release] en wijs deze toe aan de eigenschap [CloudJob.JobReleaseTask][net_job_prep_cloudjob] van uw taak om de releasetaak van de taak in te stellen.

In dit codefragment `myBatchClient` is een instantie van [BatchClient][net_batch_client]en `myPool` is een bestaande groep binnen het Batch-account.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Zoals eerder vermeld, wordt de releasetaak uitgevoerd wanneer een taak wordt beëindigd of verwijderd. Een taak beëindigen met [JobOperations.TerminateJobAsync][net_job_terminate]. Een taak verwijderen met [JobOperations.DeleteJobAsync][net_job_delete]. U beëindigt of verwijdert een taak meestal wanneer de taken zijn voltooid of wanneer een door u gedefinieerde time-out is bereikt.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Codevoorbeeld op GitHub
Als u taakvoorbereidingen en releasetaken in actie wilt zien, raadpleegt u het [JobPrepRelease-voorbeeldproject][job_prep_release_sample] op GitHub. Deze consoletoepassing doet het volgende:

1. Hiermee maakt u een pool met twee knooppunten.
2. Hiermee maakt u een taak met taakvoorbereiding, release en standaardtaken.
3. Hiermee wordt de taakvoor het voorbereiden van taken uitgevoerd, waarbij eerst de knooppunt-id wordt geschreven naar een tekstbestand in de map 'gedeeld' van een knooppunt.
4. Hiermee wordt een taak uitgevoerd op elk knooppunt dat de taak-id naar hetzelfde tekstbestand schrijft.
5. Zodra alle taken zijn voltooid (of de time-out is bereikt), drukt u de inhoud van het tekstbestand van elk knooppunt af op de console.
6. When the job is completed, runs the job release task to delete the file from the node.
7. Hiermee worden de exitcodes van de taakvoorbereiding en releasetaken afgedrukt voor elk knooppunt waarop ze zijn uitgevoerd.
8. Hiermee wordt de uitvoering onderbroken om bevestiging van taak- en/of poolverwijdering mogelijk te maken.

De uitvoer van de voorbeeldaanvraag is vergelijkbaar met:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> Vanwege de variabele creatie en begintijd van knooppunten in een nieuwe groep (sommige knooppunten zijn klaar voor taken voordat andere), ziet u mogelijk verschillende uitvoer. In het bijzonder, omdat de taken snel zijn voltooid, kan een van de knooppunten van de groep alle taken van de taak uitvoeren. Als dit gebeurt, zult u merken dat de taakprep- en releasetaken niet bestaan voor het knooppunt dat geen taken heeft uitgevoerd.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Taken voor het voorbereiden en vrijgeven van taken in de Azure-portal inspecteren
Wanneer u de voorbeeldtoepassing uitvoert, u de [Azure-portal][portal] gebruiken om de eigenschappen van de taak en de bijbehorende taken weer te geven of zelfs het gedeelde tekstbestand downloaden dat wordt gewijzigd door de taken van de taak.

De onderstaande schermafbeelding toont het **paginapuwentaken** voorbereiden in de Azure-portal na een run van de voorbeeldtoepassing. Navigeer naar de eigenschappen *JobPrepReleaseSampleJob* nadat uw taken zijn voltooid (maar voordat u uw taak en groep verwijderd) en klik op **Voorbereidingstaken** of **Taken vrijgeven** om hun eigenschappen weer te geven.

![Eigenschappen voor taakvoorbereiding in Azure-portal][1]

## <a name="next-steps"></a>Volgende stappen
### <a name="application-packages"></a>Toepassingspakketten
Naast de taakvoorbereidingstaak u ook de functie [toepassingspakketten](batch-application-packages.md) van Batch gebruiken om compute-knooppunten voor te bereiden op taakuitvoering. Deze functie is vooral handig voor het implementeren van toepassingen waarvoor geen installatieprogramma hoeft te worden uitgevoerd, toepassingen die veel (100+)-bestanden bevatten of toepassingen waarvoor strikt versiebeheer vereist is.

### <a name="installing-applications-and-staging-data"></a>Toepassingen en faseringsgegevens installeren
Deze MSDN forum post biedt een overzicht van verschillende methoden voor het voorbereiden van uw knooppunten voor het uitvoeren van taken:

[Toepassingen en faseringsgegevens installeren op batchcomputenodes][forum_post]

Geschreven door een van de Azure Batch-teamleden, bespreekt het verschillende technieken die u gebruiken om toepassingen en gegevens te implementeren om knooppunten te berekenen.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
