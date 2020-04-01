---
title: Grote hoeveelheden willekeurige gegevens parallel aan Azure Storage uploaden
description: Meer informatie over het gebruik van de Azure Storage-clientbibliotheek om grote hoeveelheden willekeurige gegevens parallel aan een Azure Storage-account te uploaden
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: dd87e1a9bcff55813dff420976df58351386fb34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75371935"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Grote hoeveelheden willekeurige gegevens gelijktijdig uploaden naar Azure Storage

Deze zelfstudie is deel twee van een serie. Deze zelfstudie laat zien hoe u een toepassing kunt implementeren waarmee grote hoeveelheden willekeurige gegevens naar een Azure Storage-account kunnen worden geüpload.

In deel twee van de serie leert u het volgende:

> [!div class="checklist"]
> * De verbindingsreeks configureren
> * De toepassing bouwen
> * De toepassing uitvoeren
> * Het aantal verbindingen valideren

Azure Blob-opslag biedt een schaalbare service voor het opslaan van gegevens. Om er zeker van te kunnen zijn of uw toepassing optimaal presteert, is het raadzaam te weten hoe blob-opslag werkt. Kennis van de limieten voor Azure-blobs is belangrijk, voor meer informatie over deze limieten bezoek: [Schaalbaarheid en prestatiedoelen voor Blob-opslag](../blobs/scalability-targets.md).

[Partitienaamgeving](../blobs/storage-performance-checklist.md#partitioning) is een andere potentieel belangrijke factor bij het ontwerpen van een krachtige toepassing met behulp van blobs. Voor blokgroottes groter dan of gelijk aan 4 MiB worden [blokblobs](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) met hoge doorvoer gebruikt en de naamgeving van partities heeft geen invloed op de prestaties. Voor blokformaten van minder dan 4 MiB maakt Azure-opslag gebruik van een op bereik gebaseerd partitieschema om de balans te schalen en te laden. Deze configuratie betekent dat bestanden met vergelijkbare naamconventies of voorvoegsels naar dezelfde partitie gaan. Deze logica bevat de naam van de container waar de bestanden naar worden geüpload. In deze zelfstudie gebruikt u de bestanden die GUID's voor namen en willekeurig gegenereerde inhoud bevatten. Deze worden vervolgens naar vijf verschillende containers met willekeurige namen geüpload.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie, moet u de vorige zelfstudie over opslag hebben voltooid: [Create a virtual machine and storage account for a scalable application][previous-tutorial] (Een virtuele machine en opslagaccount voor een schaalbare toepassing maken).

## <a name="remote-into-your-virtual-machine"></a>Extern verbinding maken met uw virtuele machine

Gebruik de volgende opdracht op uw lokale machine om een sessie met een extern bureaublad te starten voor de virtuele machine. Vervang het IP-adres door het publicIPAddress van de virtuele machine. Wanneer u hierom wordt gevraagd, typt u de referenties die u hebt gebruikt bij het maken van de virtuele machine.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>De verbindingsreeks configureren

Ga in Azure Portal naar het nieuwe opslagaccount. Selecteer bij **Instellingen** in uw opslagaccount de optie **Toegangssleutels**. Kopieer de **verbindingsreeks** uit de primaire of secundaire sleutel. Meld u aan bij de virtuele machine die u tijdens de vorige zelfstudie hebt gemaakt. Open een **Opdrachtprompt** als beheerder en voer de opdracht `setx` uit met de `/m`-switch. Met deze opdracht wordt een omgevingsvariabele van een machine-instelling opgeslagen. De omgevingsvariabele wordt pas beschikbaar wanneer u de **Opdrachtprompt** opnieuw laadt. ** \<StorageConnectionString\> vervangen** in het volgende voorbeeld:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

Wanneer u klaar bent, opent u een andere **Opdrachtprompt**, navigeert u naar `D:\git\storage-dotnet-perf-scale-app` en typt u `dotnet build` om de toepassing te bouwen.

## <a name="run-the-application"></a>De toepassing uitvoeren

Navigeer naar `D:\git\storage-dotnet-perf-scale-app`.

Typ `dotnet run` om de toepassing uit te voeren. De eerste keer dat u `dotnet` uitvoert, wordt uw lokale pakketcache gevuld, waardoor de snelheid voor het terugzetten van back-ups verbetert en offlinetoegang mogelijk is. De uitvoering van deze opdracht duurt maximaal een minuut en is eenmalig.

```
dotnet run
```

De toepassing maakt vijf containers met willekeurige namen en begint met het uploaden van de bestanden in de staging-map naar het opslagaccount. De toepassing stelt het minimumaantal threads in op 100 en de [DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) op 100 om ervoor te zorgen dat een groot aantal gelijktijdige verbindingen zijn toegestaan wanneer de toepassing wordt uitgevoerd.

Naast het instellen van de limietinstellingen voor threads en verbindingen zijn de [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) voor de methode [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) geconfigureerd om gebruik te maken van parallelle uitvoering en om validatie van de MD5-hash uit te schakelen. De bestanden worden geüpload in blokken van 100 MB. Deze configuratie biedt betere prestaties maar kan duur zijn als er een slecht presterend netwerk wordt gebruikt, omdat, wanneer er zich een storing voordoet, het gehele blok van 100 MB opnieuw wordt geüpload.

|Eigenschap|Waarde|Beschrijving|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| Met deze instelling wordt de blob in blokken opgesplitst bij het uploaden. Voor de hoogste prestaties moet deze waarde acht keer het aantal cores zijn. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| waar| Met deze eigenschap wordt de controle uitgeschakeld van de MD5-hash van de inhoud die wordt geüpload. MD5-validatie zorgt voor een snellere overdracht. Maar hiermee wordt de geldigheid of de integriteit van de bestanden die worden overgebracht, niet bevestigd.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| Deze eigenschap bepaalt of een MD5-hash wordt berekend en samen met het bestand opgeslagen.   |
| [Opnieuw proberenBeleid](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| Twee seconden uitstel bij maximaal tien nieuwe pogingen |Hiermee bepaalt u het beleid voor het opnieuw proberen van aanvragen. Bij verbindingsfouten wordt opnieuw geprobeerd. In dit voorbeeld is een [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry)-beleid geconfigureerd met een uitstel van twee seconden en een maximumaantal nieuwe pogingen van 10. Deze instelling is belangrijk wanneer uw toepassing dicht bij het bereiken van de schaalbaarheidsdoelen voor Blob-opslag komt. Zie [Schaalbaarheids- en prestatiedoelen voor Blob-opslag voor](../blobs/scalability-targets.md)meer informatie.  |

De taak `UploadFilesAsync` wordt in het volgende voorbeeld weergegeven:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

Het volgende voorbeeld bevat de afgekapte uitvoer van een toepassing die op een Windows-systeem wordt uitgevoerd.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>De verbindingen valideren

Terwijl de bestanden worden geüpload, kunt u controleren hoeveel gelijktijdige verbindingen naar uw opslagaccount er zijn. Open een **opdrachtprompt** en typ `netstat -a | find /c "blob:https"`. Deze opdracht geeft u het aantal verbindingen die momenteel zijn geopend met behulp van `netstat`. Het volgende voorbeeld laat uitvoer zien die te vergelijken is met wat u ziet als u de zelfstudie zelf uitvoert. Zoals u in het voorbeeld kunt zien, waren er 800 verbindingen geopend toen de willekeurige bestanden naar het opslagaccount werden geüpload. Deze waarde wijzigt tijdens het uploaden. Door blokken in brokken gelijktijdig te uploaden, wordt de hoeveelheid tijd die nodig is om inhoud over te dragen, aanzienlijk lager.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Volgende stappen

In deel twee uit de serie bent u meer te weten gekomen over het gelijktijdig uploaden van grote hoeveelheden willekeurige gegevens naar een opslagaccount, om het volgende te kunnen doen:

> [!div class="checklist"]
> * De verbindingsreeks configureren
> * De toepassing bouwen
> * De toepassing uitvoeren
> * Het aantal verbindingen valideren

Ga verder met deel drie van de serie als u grote hoeveelheden gegevens uit een opslagaccount wilt downloaden.

> [!div class="nextstepaction"]
> [Grote hoeveelheden willekeurige gegevens downloaden uit Azure Storage](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
