---
title: Uitvoergegevens blijven naar Azure Storage met de .NET-bestandsconventiesbibliotheek - Azure Batch
description: Lees hoe u de bibliotheek Azure Batch File Conventions gebruikt voor .NET om batchtaak & taakuitvoer naar Azure Storage voort te houden en deze uitvoer weer te geven in de Azure-portal.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 693017e529f2869c16d94c30cdf48ec228df3276
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022865"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Werk- en taakgegevens blijven uitvoeren in Azure Storage met de bibliotheek BatchFile Conventions voor .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Een manier om taakgegevens voort te houden, is door de [Azure Batch File Conventions-bibliotheek te][nuget_package]gebruiken voor .NET . De bibliotheek Bestandsconventies vereenvoudigt het proces van het opslaan van taakuitvoergegevens naar Azure Storage en het ophalen ervan. U de bibliotheek Bestandsconventies gebruiken &mdash; in zowel taak- als clientcode in taakcode voor doorgaande bestanden en in clientcode om deze weer te geven en op te halen. Uw taakcode kan de bibliotheek ook gebruiken om de uitvoer van upstreamtaken op te halen, zoals in een scenario [voor taakafhankelijkheden.](batch-task-dependencies.md)

Als u uitvoerbestanden wilt ophalen met de bibliotheek Bestandsconventies, u de bestanden voor een bepaalde taak of taak vinden door ze op id en doel op te sommen. U hoeft de namen of locaties van de bestanden niet te kennen. U bijvoorbeeld de bibliotheek Bestandsconventies gebruiken om alle tussenliggende bestanden voor een bepaalde taak weer te geven of een voorbeeldbestand voor een bepaalde taak op te vragen.

> [!TIP]
> Vanaf versie 2017-05-01 ondersteunt de Batch-service-API aanhoudende uitvoergegevens naar Azure Storage voor taken en taakbeheertaken die worden uitgevoerd op groepen die zijn gemaakt met de configuratie van de virtuele machine. De Batch service API biedt een eenvoudige manier om uitvoer voort te houden vanuit de code die een taak maakt en dient als alternatief voor de bibliotheek Bestandsconventies. U uw Batch-clienttoepassingen aanpassen om de uitvoer te blijven uitvoeren zonder dat u de toepassing die uw taak uitvoert, hoeft bij te werken. Zie [Taakgegevens voor Azure-opslag voor](batch-task-output-files.md)meer informatie voor meer informatie.

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Wanneer gebruik ik de bibliotheek Bestandsconventies om de taakuitvoer voort te houden?

Azure Batch biedt meer dan één manier om taakuitvoer voort te duren. De bestandsverdragen zijn het meest geschikt voor deze scenario's:

- U de code voor de toepassing die uw taak uitvoert eenvoudig wijzigen om bestanden te blijven bestaan met behulp van de bibliotheek Bestandsconventies.
- U wilt gegevens streamen naar Azure Storage terwijl de taak nog steeds wordt uitgevoerd.
- U wilt gegevens van groepen die zijn gemaakt met de cloudserviceconfiguratie of de configuratie van de virtuele machine, blijven bestaan.
- Uw clienttoepassing of andere taken in de taak moeten taakuitvoerbestanden vinden en downloaden op id of doel.
- U wilt taakuitvoer weergeven in de Azure-portal.

Als uw scenario afwijkt van de hierboven genoemde scenario's, moet u mogelijk een andere aanpak overwegen. Zie Taak- en taakuitvoer voormeer informatie over andere opties voor het uitvoeren van taken, [raadpleegt Blijvende taak- en taakuitvoer naar Azure Storage](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>Wat is de batchbestandsconventiesstandaard?

De standaard Batch File Conventions biedt een [naamgevingsschema](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files) voor de doelcontainers en blobpaden waarnaar uw uitvoerbestanden zijn geschreven. Bestanden die volbleven bij Azure Storage en die voldoen aan de standaard Bestandsconventies, kunnen automatisch worden weergegeven in de Azure-portal. Het portaal is op de hoogte van de naamgevingsconventie en kan dus bestanden weergeven die eraan voldoen.

De bibliotheek Bestandsconventies voor .NET geeft automatisch een naam aan uw opslagcontainers en taakuitvoerbestanden volgens de standaard Bestandsconventies. De bibliotheek Bestandsconventies biedt ook methoden voor het opvragen van uitvoerbestanden in Azure Storage op basis van taak-id, taak-id of doel.

Als u zich ontwikkelt met een andere taal dan .NET, u de standaard Bestandsconventies zelf implementeren in uw toepassing. Zie [De standaard BatchFile Conventions implementeren voor](batch-task-output.md#implement-the-batch-file-conventions-standard)meer informatie.

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Een Azure Storage-account koppelen aan uw Batch-account

Als u uitvoergegevens wilt blijven gebruiken voor Azure Storage via de bibliotheek Bestandsconventies, moet u eerst een Azure Storage-account koppelen aan uw Batch-account. Als u dit nog niet hebt gedaan, koppelt u een opslagaccount aan uw Batch-account met behulp van de [Azure-portal:](https://portal.azure.com)

1. Ga in Azure Portal naar uw Batch-account.
1. Selecteer **onder Instellingen**de optie **Opslagaccount**.
1. Als u nog geen opslagaccount hebt dat is gekoppeld aan uw Batch-account, klikt u op **Opslagaccount (Geen)**.
1. Selecteer een Opslagaccount in de lijst voor uw abonnement. Gebruik voor de beste prestaties een Azure Storage-account dat zich in dezelfde regio bevindt als het Batch-account waar uw taken worden uitgevoerd.

## <a name="persist-output-data"></a>Uitvoergegevens blijven bestaan

Als u taak- en taakuitvoergegevens wilt blijven uitvoeren met de bibliotheek Bestandsconventies, maakt u een container in Azure Storage en slaat u de uitvoer op in de container. Gebruik de [Azure Storage-clientbibliotheek voor .NET](https://www.nuget.org/packages/WindowsAzure.Storage) in uw taakcode om de taakuitvoer naar de container te uploaden.

Zie [Aan de slag met Azure Blob-opslag met .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Alle taak- en taakuitvoerdie zijn blijven bestaan met de bibliotheek Bestandsconventies, worden in dezelfde container opgeslagen. Als een groot aantal taken tegelijkertijd bestanden probeert voort te houden, kunnen azure-opslagbeperkingslimieten worden afgedwongen. Zie checklist prestaties en schaalbaarheid [voor Blob-opslag voor](../storage/blobs/storage-performance-checklist.md)meer informatie over beperkingslimieten.

### <a name="create-storage-container"></a>Opslagcontainer maken

Als u taakuitvoer naar Azure Storage wilt blijven uitvoeren, maakt u eerst een container door [CloudJob][net_cloudjob]aan te roepen. [VoorbereidenOutputStorageAsync][net_prepareoutputasync]. Met deze extensiemethode wordt een [CloudStorageAccount-object][net_cloudstorageaccount] als parameter gebruikt. Hiermee wordt een container gemaakt met de naam Bestandconventies, zodat de inhoud ervan kan worden ontdekt door de Azure-portal en de methoden voor het ophalen die later in het artikel worden besproken.

U plaatst de code meestal om een &mdash; container in uw clienttoepassing te maken, de toepassing die uw groepen, taken en taken maakt.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Taakuitvoer opslaan

Nu u een container in Azure Storage hebt voorbereid, kunnen taken uitvoer naar de container opslaan met behulp van de klasse [TaskOutputStorage][net_taskoutputstorage] in de bibliotheek Bestandsconventies.

Maak in uw taakcode eerst een object [TaskOutputStorage][net_taskoutputstorage] en roep vervolgens de [Taakuitvoeropslag][net_taskoutputstorage]aan wanneer de taak zijn werk heeft voltooid. [SaveAsync-methode][net_saveasync] om de uitvoer op te slaan naar Azure Storage.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

De `kind` parameter van de [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage). [SaveAsync-methode](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) categoriseert de blijvende bestanden. Er zijn vier vooraf gedefinieerde [TaakOutputKind-typen:][net_taskoutputkind] `TaskOutput`, `TaskPreview`, `TaskLog`en `TaskIntermediate.` u ook aangepaste uitvoercategorieën definiëren.

Met deze uitvoertypen u opgeven welk type uitvoer u wilt weergeven wanneer u batch later opvraagt voor de aanhoudende uitvoer van een bepaalde taak. Met andere woorden, wanneer u de uitvoer voor een taak aanlevert, u de lijst filteren op een van de uitvoertypen. Bijvoorbeeld: 'Geef me de *voorbeelduitvoer* voor taak *109'.* Meer informatie over het aanbieden en ophalen van uitvoer wordt later in het artikel weergegeven in Uitvoer ophalen.

> [!TIP]
> De uitvoersoort bepaalt ook waar in de Azure-portal een bepaald bestand wordt weergegeven: *Taakuitvoer-gecategoriseerde*bestanden worden weergegeven onder **Taakuitvoerbestanden**en *Taaklog-bestanden* worden weergegeven onder **Taaklogboeken**.

### <a name="store-job-outputs"></a>Taakuitvoer opslaan

Naast het opslaan van taakuitvoer, u de uitvoer opslaan die aan een hele taak zijn gekoppeld. In de samenvoegtaak van een filmrenderingtaak u bijvoorbeeld doorgaan met de volledig gerenderde film als taakuitvoer. Wanneer uw taak is voltooid, kan uw clienttoepassing de uitvoer voor de taak weergeven en ophalen en hoeft u de afzonderlijke taken niet op te vragen.

Sla taakuitvoer op door de [JobOutputStorage][net_joboutputstorage]aan te roepen . [SaveAsync-methode][net_joboutputstorage_saveasync] en geef de [Taakuitvoerkind][net_joboutputkind] en bestandsnaam op:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Net als bij het **type Taakuitvoerkind** voor taakuitvoer, gebruikt u het type [JobOutputKind][net_joboutputkind] om de aanhoudende bestanden van een taak te categoriseren. Met deze parameter u later een specifiek type uitvoer opvragen (een lijst). Het type **JobOutputKind** bevat zowel uitvoer- als voorbeeldcategorieën en ondersteunt het maken van aangepaste categorieën.

### <a name="store-task-logs"></a>Taaklogboeken opslaan

Naast het aanhouden van een bestand tot duurzame opslag wanneer een taak of taak is voltooid, &mdash; moet `stdout.txt` u `stderr.txt`mogelijk ook bestanden blijven staan die worden bijgewerkt tijdens de uitvoering van een taaklogboekbestanden of, bijvoorbeeld. Hiervoor biedt de bibliotheek Azure Batch File Conventions de [Taakuitvoeropslag][net_taskoutputstorage]. [SaveTrackedAsync-methode.][net_savetrackedasync] Met [SaveTrackedAsync][net_savetrackedasync]u updates voor een bestand op het knooppunt bijhouden (met een interval dat u opgeeft) en deze updates voorAzure Storage behouden.

In het volgende codefragment gebruiken we [SaveTrackedAsync][net_savetrackedasync] om de 15 seconden tijdens de uitvoering van de taak bij te werken `stdout.txt` in Azure Storage:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

De opmerkingssectie `Code to process data and produce output file(s)` is een tijdelijke aanduiding voor de code die uw taak normaal gesproken zou uitvoeren. U bijvoorbeeld code hebben die gegevens uit Azure Storage downloadt en er transformatie of berekening op uitvoert. Het belangrijkste deel van dit fragment laat zien hoe `using` u dergelijke code in een blok verpakken om een bestand periodiek bij te werken met [SaveTrackedAsync.][net_savetrackedasync]

De knooppuntagent is een programma dat op elk knooppunt in de groep wordt uitgevoerd en de interface tussen opdracht en controle biedt tussen het knooppunt en de batchservice. De `Task.Delay` oproep is vereist aan `using` het einde van dit blok om ervoor te zorgen dat de node agent tijd heeft om de inhoud van de standaard door te spoelen naar de stdout.txt bestand op het knooppunt. Zonder deze vertraging is het mogelijk om de laatste paar seconden van de output te missen. Deze vertraging is mogelijk niet vereist voor alle bestanden.

> [!NOTE]
> Wanneer u bestandstracking inschakelt met **SaveTrackedAsync,** blijven alleen *de toevoegen aan* het bijgehouden bestand bestaan uit Azure Storage. Gebruik deze methode alleen voor het bijhouden van niet-roterende logboekbestanden of andere bestanden die zijn geschreven met toemeldingen tot het einde van het bestand.

## <a name="retrieve-output-data"></a>Uitvoergegevens ophalen

Wanneer u uw uitvoer voortduurt met de Azure Batch File Conventions-bibliotheek, doet u dit op een taak- en taakgerichte manier. U de uitvoer voor een bepaalde taak of taak aanvragen zonder dat u het pad in Azure Storage of zelfs de bestandsnaam hoeft te kennen. In plaats daarvan u uitvoerbestanden aanvragen op taak of taak-id.

Het volgende codefragment doorloopt de taken van een taak, drukt wat informatie af over de uitvoerbestanden voor de taak en downloadt vervolgens de bestanden uit Opslag.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Uitvoerbestanden weergeven in de Azure-portal

De Azure-portal geeft taakuitvoerbestanden en -logboeken weer die worden weergegeven aan een gekoppeld Azure Storage-account met behulp van de [batchbestandsconventiesstandaard.](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) U deze conventies zelf implementeren in de taal van uw keuze, of u de bibliotheek Bestandsconventies gebruiken in uw .NET-toepassingen.

Als u de weergave van uw uitvoerbestanden in de portal wilt inschakelen, moet u aan de volgende vereisten voldoen:

1. Een Azure Storage-account koppelen aan uw Batch-account.
1. Houd u aan de vooraf gedefinieerde naamgevingsconventies voor opslagcontainers en -bestanden wanneer de uitvoer voortduurt. U vindt de definitie van deze conventies in de file conventions bibliotheek [README][github_file_conventions_readme]. Als u de bibliotheek [Azure Batch File Conventions][nuget_package] gebruikt om uw uitvoer voort te zetten, blijven uw bestanden bestaan volgens de standaard Bestandsconventies.

Als u taakuitvoerbestanden en logboeken in de Azure-portal wilt weergeven, navigeert u naar de taak waarin u geïnteresseerd bent en klikt u vervolgens op **Opgeslagen uitvoerbestanden** of **Opgeslagen logboeken**. Deze afbeelding toont de **opgeslagen uitvoerbestanden** voor de taak met ID "007":

![Taakuitvoerblad in de Azure-portal][2]

## <a name="code-sample"></a>Codevoorbeeld

Het voorbeeldproject [PersistOutputs][github_persistoutputs] is een van de [Azure Batch-codevoorbeelden][github_samples] op GitHub. Deze Visual Studio-oplossing laat zien hoe u de azure batchbestandsconventiesbibliotheek gebruiken om taakuitvoer naar duurzame opslag voort te zetten. Voer de volgende stappen uit om het voorbeeld uit te voeren:

1. Open het project in **Visual Studio 2019**.
2. Voeg uw batch- en **opslagaccountreferenties** toe aan **AccountInstellingen.-instellingen** in het project Microsoft.Azure.Batch.Samples.Common.
3. **Bouw** (maar voer niet) de oplossing uit. Herstel eventuele NuGet-pakketten als daarom wordt gevraagd.
4. Gebruik de Azure-portal om een [toepassingspakket](batch-application-packages.md) voor **PersistOutputsTask te uploaden.** Neem `PersistOutputsTask.exe` de afhankelijke samenstellingen en onderdelen op in het .zip-pakket, stel de toepassings-id in op 'PersistOutputsTask' en de versie van het toepassingspakket op '1.0'.
5. **Start** (run) het **persistoutputproject.**
6. Wanneer u wordt gevraagd om de persistentietechnologie te kiezen die moet worden gebruikt voor het uitvoeren van het voorbeeld, voert **u 1** in om het voorbeeld uit te voeren met behulp van de bibliotheek Bestandsconventies om taakuitvoer voort te zetten. 

## <a name="next-steps"></a>Volgende stappen

### <a name="get-the-batch-file-conventions-library-for-net"></a>Download de bibliotheek Batch File Conventions voor .NET

De bibliotheek Batch File Conventions voor .NET is beschikbaar op [NuGet.][nuget_package] De bibliotheek breidt de [cloudjob-][net_cloudjob] en [CloudTask-klassen][net_cloudtask] uit met nieuwe methoden. Zie ook de [referentiedocumentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) voor de bibliotheek Bestandsconventies.

De [broncode][github_file_conventions] voor de bibliotheek Bestandsconventies is beschikbaar op GitHub in de Microsoft Azure SDK voor .NET-repository. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Andere benaderingen verkennen voor aanhoudende uitvoergegevens

- Zie [Taak- en taakuitvoer voorhouden naar Azure Storage](batch-task-output.md) voor een overzicht van doorgaande taak- en taakgegevens.
- Zie [Taakgegevens voor Azure-opslag voorhouden met de Batchservice-API](batch-task-output-files.md) voor meer informatie over het gebruik van de Batchservice-API om uitvoergegevens voort te houden.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Opgeslagen uitvoerbestanden en logboekenkiezer opslaan in portal"
[2]: ./media/batch-task-output/task-output-02.png "Taakuitvoerblad in de Azure-portal"
