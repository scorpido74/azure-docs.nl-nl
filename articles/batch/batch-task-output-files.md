---
title: Uitvoergegevens blijven naar Azure Storage met Batch service API - Azure Batch
description: Meer informatie over het gebruik van de Batchservice-API om batchtaak- en taakuitvoergegevens voor azure-opslag te behouden.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 03/05/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 11bd8bc427dd3da35ec5aa0f728f6b04b7d4527d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022848"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Taakgegevens voorTduren in Azure Storage met de Batch service API

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

De Batch service API ondersteunt blijvende uitvoergegevens naar Azure Storage voor taken en taakbeheertaken die worden uitgevoerd op pools met de configuratie van de virtuele machine. Wanneer u een taak toevoegt, u een container in Azure Storage opgeven als bestemming voor de uitvoer van de taak. De batchservice schrijft vervolgens alle uitvoergegevens naar die container wanneer de taak is voltooid.

Een voordeel van het gebruik van de Batch service API om taakuitvoer voort te zetten, is dat u de toepassing die de taak uitvoert niet hoeft te wijzigen. In plaats daarvan u met een paar wijzigingen in uw clienttoepassing de uitvoer van de taak blijven uitvoeren vanuit dezelfde code die de taak maakt.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Wanneer gebruik ik de Batch service API om de taakuitvoer voort te zetten?

Azure Batch biedt meer dan één manier om taakuitvoer voort te duren. Het gebruik van de Batch service API is een handige aanpak die het meest geschikt is voor deze scenario's:

- U wilt code schrijven om taakuitvoer vanuit uw clienttoepassing voort te houden, zonder de toepassing te wijzigen die uw taak uitvoert.
- U wilt de uitvoer van batchtaken en taakbeheertaken in groepen die zijn gemaakt met de configuratie van de virtuele machine, behouden.
- U wilt de uitvoer naar een Azure Storage-container met een willekeurige naam blijven gebruiken.
- U wilt de uitvoer naar een Azure Storage-container met de naam [batchbestandsconventies](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)blijven gebruiken. 

Als uw scenario afwijkt van de hierboven genoemde scenario's, moet u mogelijk een andere aanpak overwegen. De Batchservice-API biedt momenteel bijvoorbeeld geen ondersteuning voor streaminguitvoer naar Azure Storage terwijl de taak wordt uitgevoerd. Als u uitvoer wilt streamen, u overwegen de bibliotheek BatchBestandsconventies te gebruiken die beschikbaar is voor .NET. Voor andere talen moet u uw eigen oplossing implementeren. Zie Taak- en taakuitvoer voormeer informatie over andere opties voor het uitvoeren van taken, [raadpleegt Blijvende taak- en taakuitvoer naar Azure Storage](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Een container maken in Azure Storage

Als u taakuitvoer naar Azure Storage wilt blijven uitvoeren, moet u een container maken die fungeert als bestemming voor uw uitvoerbestanden. Maak de container voordat u uw taak uitvoert, bij voorkeur voordat u uw taak indient. Als u de container wilt maken, gebruikt u de juiste Azure Storage-clientbibliotheek of SDK. Zie de [Azure Storage-documentatie](https://docs.microsoft.com/azure/storage/)voor meer informatie over Azure Storage API's.

Als u bijvoorbeeld uw toepassing schrijft in C#, gebruikt u de [Azure Storage-clientbibliotheek voor .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). In het volgende voorbeeld ziet u hoe u een container maakt:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Een handtekening voor gedeelde toegang voor de container

Nadat u de container hebt gemaakt, krijgt u een sas (shared access signature) met schrijftoegang tot de container. Een SAS biedt gedelegeerde toegang tot de container. De SAS verleent toegang met een bepaalde set machtigingen en over een opgegeven tijdsinterval. De batchservice heeft een SAS met schrijfmachtigingen nodig om taakuitvoer naar de container te schrijven. Zie [SAS \(\) ](../storage/common/storage-dotnet-shared-access-signature-part-1.md)voor gedeelde toegangshandtekeningen gebruiken in Azure Storage voor meer informatie over SAS.

Wanneer u een SAS krijgt met de Azure Storage API's, retourneert de API een SAS-tekenreeks. Deze tekenreeks bevat alle parameters van de SAS, inclusief de machtigingen en het interval waarop de SAS geldig is. Als u de SAS wilt gebruiken om toegang te krijgen tot een container in Azure Storage, moet u de SAS-tekenreeks toevoegen aan de bron-URI. De resource URI biedt samen met het aan een app en meer toegevoegen SAS-token geverifieerde toegang tot Azure Storage.

In het volgende voorbeeld ziet u hoe u een sas-tekenreeks voor alleen schrijven voor de container krijgen en voegt u de SAS toe aan de container URI:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Uitvoerbestanden opgeven voor taakuitvoer

Als u uitvoerbestanden voor een taak wilt opgeven, maakt u een verzameling [OutputFile-objecten](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) en wijst u deze toe aan de eigenschap [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) wanneer u de taak maakt.

In het volgende voorbeeld van C#-code wordt `output.txt`een taak uitgevoerd die willekeurige getallen schrijft naar een bestand met de naam . In het voorbeeld wordt `output.txt` een uitvoerbestand voor geschreven naar de container. In het voorbeeld worden ook uitvoerbestanden aanmaakt `std*.txt` voor logboekbestanden die `stderr.txt`overeenkomen met het bestandspatroon _(bijvoorbeeld_en `stdout.txt` ). De URL van de container vereist de SAS die eerder voor de container is gemaakt. De Batch-service gebruikt de SAS om de toegang tot de container te verifiëren:

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

### <a name="specify-a-file-pattern-for-matching"></a>Een bestandspatroon opgeven voor matching

Wanneer u een uitvoerbestand opgeeft, u de eigenschap [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) gebruiken om een bestandspatroon voor matching op te geven. Het bestandspatroon kan overeenkomen met nul bestanden, één bestand of een set bestanden die door de taak zijn gemaakt.

De eigenschap **FilePattern** ondersteunt standaard wildcards van bestandssysteem, zoals `*` (voor niet-recursieve overeenkomsten) en `**` (voor recursieve overeenkomsten). In het bovenstaande codevoorbeeld wordt bijvoorbeeld `std*.txt` het bestandspatroon opgegeven dat niet-recursief overeenkomt:

`filePattern: @"..\std*.txt"`

Als u één bestand wilt uploaden, geeft u een bestandspatroon op zonder jokertekens. In het bovenstaande codevoorbeeld wordt bijvoorbeeld `output.txt`het bestandspatroon opgegeven dat overeenkomt met:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Een uploadvoorwaarde opgeven

De eigenschap [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) maakt het mogelijk om uitvoerbestanden op voorwaardelijke basis te uploaden. Een veelvoorkomend scenario is het uploaden van één set bestanden als de taak slaagt en een andere set bestanden als deze mislukt. U bijvoorbeeld alleen uitgebreide logboekbestanden uploaden wanneer de taak mislukt en wordt afgesloten met een niet-nulexitcode. Op dezelfde manier u alleen resultaatbestanden uploaden als de taak slaagt, omdat deze bestanden mogelijk ontbreken of onvolledig zijn als de taak mislukt.

In het bovenstaande codevoorbeeld wordt de eigenschap **UploadCondition** ingesteld op **Taakvoltooiing**. Deze instelling geeft aan dat het bestand moet worden geüpload nadat de taken zijn voltooid, ongeacht de waarde van de exitcode.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Zie het nlum [OutputFileUploadCondition voor](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) andere instellingen.

### <a name="disambiguate-files-with-the-same-name"></a>Disambiguate-bestanden met dezelfde naam

De taken in een taak kunnen bestanden met dezelfde naam produceren. Bijvoorbeeld, `stdout.txt` en `stderr.txt` worden gemaakt voor elke taak die wordt uitgevoerd in een taak. Omdat elke taak in zijn eigen context wordt uitgevoerd, komen deze bestanden niet in conflict met het bestandssysteem van het knooppunt. Wanneer u echter bestanden van meerdere taken uploadt naar een gedeelde container, moet u bestanden met dezelfde naam disambiguateren.

De eigenschap [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) geeft de doelblob of virtuele map voor uitvoerbestanden op. U de eigenschap **Pad** gebruiken om de blob of virtuele map een naam te geven op een zodanige manier dat uitvoerbestanden met dezelfde naam uniek worden genoemd in Azure Storage. Het gebruik van de taak-ID op het pad is een goede manier om unieke namen te garanderen en bestanden eenvoudig te identificeren.

Als de eigenschap **FilePattern** is ingesteld op een wildcard-expressie, worden alle bestanden die overeenkomen met het patroon geüpload naar de virtuele map die is opgegeven door de eigenschap **Path.** Als de container bijvoorbeeld `mycontainer`de taak-id is `mytask`en `..\std*.txt`het bestandspatroon is, zijn de absolute URI's voor de uitvoerbestanden in Azure Storage vergelijkbaar met:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Als de eigenschap **FilePattern** overeenkomt met één bestandsnaam, wat betekent dat deze geen jokertekens bevat, geeft de waarde van de eigenschap **Pad** de volledig gekwalificeerde blobnaam op. Als u verwacht dat de naamgeving in strijd is met één bestand van meerdere taken, neemt u de naam van de virtuele map op als onderdeel van de bestandsnaam om deze bestanden te disambiguateren. Stel bijvoorbeeld de eigenschap **Pad** in om de taak-id, het scheidingstekenteken (meestal een slash vooruit) en de bestandsnaam op te nemen:

`path: taskId + @"/output.txt"`

De absolute URI's voor de uitvoerbestanden voor een reeks taken zijn vergelijkbaar met:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Zie [De blobs in een container weergeven](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container)voor meer informatie over virtuele mappen in Azure Storage.

## <a name="diagnose-file-upload-errors"></a>Fouten in het uploaden van bestanden diagnosticeren

Als het uploaden van uitvoerbestanden naar Azure Storage mislukt, wordt de taak verplaatst naar de **status Voltooid** en wordt de eigenschap [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) ingesteld. Controleer de eigenschap **FailureInformation** om te bepalen welke fout is opgetreden. Hier treedt bijvoorbeeld een fout op bij het uploaden van bestanden als de container niet kan worden gevonden:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Op elke bestandsupload schrijft Batch twee logbestanden `fileuploadout.txt` naar `fileuploaderr.txt`het compute-knooppunt en . U deze logboekbestanden onderzoeken om meer te weten te komen over een specifieke fout. In gevallen waarin het uploaden van het bestand nooit is geprobeerd, bijvoorbeeld omdat de taak zelf niet kon worden uitgevoerd, zullen deze logboekbestanden niet bestaan.

## <a name="diagnose-file-upload-performance"></a>Diagnose van de prestaties van het uploaden van bestanden

De `fileuploadout.txt` voortgang van het uploaden van de bestandslogboeken. U dit bestand onderzoeken voor meer informatie over hoe lang het uploaden van uw bestand duurt. Houd er rekening mee dat er veel factoren zijn die bijdragen aan het uploaden van prestaties, waaronder de grootte van het knooppunt, andere activiteit op het knooppunt op het moment van het uploaden, of de doelcontainer zich in dezelfde regio bevindt als de batchgroep, hoeveel knooppunten worden geüpload naar de opslagaccount op hetzelfde moment, enzovoort.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>De Batchservice-API gebruiken met de batchbestandsconventiesstandaard

Wanneer u taakuitvoer blijft uitvoeren met de Batch service API, u uw doelcontainer en blobs een naam geven zoals u dat wilt. U er ook voor kiezen om ze een naam te geven volgens de [batchbestandsconventiesstandaard.](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) De standaard Bestandsconventies bepaalt de namen van de doelcontainer en blob in Azure Storage voor een bepaald uitvoerbestand op basis van de namen van de taak en taak. Als u de standaard Bestandsconventies gebruikt voor het benoemen van uitvoerbestanden, zijn uw uitvoerbestanden beschikbaar om te bekijken in de [Azure-portal.](https://portal.azure.com)

Als u zich ontwikkelt in C#, u de methoden gebruiken die zijn ingebouwd in de [bibliotheek Batch File Conventions voor .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). In deze bibliotheek worden de goed benoemde containers en blobpaden voor u geopperd. U bijvoorbeeld de API aanroepen om de juiste naam voor de container te krijgen op basis van de taaknaam:

```csharp
string containerName = job.OutputStorageContainerName();
```

U de methode [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) gebruiken om een SAS-URL (Shared Access Signature) terug te sturen die wordt gebruikt om naar de container te schrijven. U deze SAS vervolgens doorgeven aan de [constructor OutputFileBlobContainerDestination.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination)

Als u zich ontwikkelt in een andere taal dan C#, moet u de standaard Bestandsconventies zelf implementeren.

## <a name="code-sample"></a>Codevoorbeeld

Het voorbeeldproject [PersistOutputs][github_persistoutputs] is een van de [Azure Batch-codevoorbeelden][github_samples] op GitHub. Deze Visual Studio-oplossing laat zien hoe u de batchclientbibliotheek gebruiken voor .NET om taakuitvoer voor duurzame opslag voort te zetten. Voer de volgende stappen uit om het voorbeeld uit te voeren:

1. Open het project in **Visual Studio 2019**.
2. Voeg uw batch- en **opslagaccountreferenties** toe aan **AccountInstellingen.-instellingen** in het project Microsoft.Azure.Batch.Samples.Common.
3. **Bouw** (maar voer niet) de oplossing uit. Herstel eventuele NuGet-pakketten als daarom wordt gevraagd.
4. Gebruik de Azure-portal om een [toepassingspakket](batch-application-packages.md) voor **PersistOutputsTask te uploaden.** Neem `PersistOutputsTask.exe` de afhankelijke samenstellingen en onderdelen op in het .zip-pakket, stel de toepassings-id in op 'PersistOutputsTask' en de versie van het toepassingspakket op '1.0'.
5. **Start** (run) het **persistoutputproject.**
6. Wanneer u wordt gevraagd om de persistentietechnologie te kiezen die moet worden gebruikt voor het uitvoeren van het voorbeeld, voert u **2** in om het voorbeeld uit te voeren met behulp van de Batchservice-API om taakuitvoer voort te zetten.
7. Voer desgewenst het voorbeeld opnieuw uit en voert **u 3** in om de uitvoer voort te houden met de Batchservice-API en ook om de doelcontainer en het blobpad een naam te geven volgens de standaard Bestandsconventies.

## <a name="next-steps"></a>Volgende stappen

- Zie [Taak- en taakgegevens voor Azure Storage](batch-task-output-file-conventions.md)blijven voor meer informatie over de uitvoer van taken voor .NET.
- Zie [Taak- en taakuitvoer](batch-task-output.md)voor azure-opslag voor informatie over andere benaderingen voor het aanhouden van uitvoergegevens in Azure Batch.

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
