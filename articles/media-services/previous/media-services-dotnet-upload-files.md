---
title: Bestanden uploaden naar een Media Services-account met .NET | Microsoft Documenten
description: Meer informatie over het verkrijgen van media-inhoud in Media Services door het maken en uploaden van assets.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 03b9995eab503ac1fcd4615882419dde31d4f8bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64869473"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Bestanden uploaden naar een Media Services-account met .NET 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

In Media Services moet u uw digitale bestanden uploaden naar (of opnemen in) een asset. De **entiteit Asset** kan video,audio, afbeeldingen, miniatuurverzamelingen, teksttracks en ondertitelingsbestanden (en de metagegevens over deze bestanden) bevatten.  Zodra de bestanden zijn geüpload, wordt uw inhoud veilig opgeslagen in de cloud voor verdere verwerking en streaming.

De bestanden in de asset worden **assetbestanden** genoemd. De **instantie AssetFile** en het werkelijke mediabestand zijn twee verschillende objecten. De instantie AssetFile bevat metagegevens over het mediabestand, terwijl het mediabestand de werkelijke media-inhoud bevat.

## <a name="considerations"></a>Overwegingen

De volgende overwegingen zijn van toepassing:
 
 * Media Services gebruikt de waarde van de eigenschap IAssetFile.Name bij het bouwen van URL's voor de streaming-inhoud (bijvoorbeeld http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Om deze reden is procentcodering niet toegestaan. De waarde van de eigenschap **Naam** kan geen van de volgende tekens hebben [die zijn gereserveerd](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();::@&=+$,/?%#[]". Ook kan er slechts één '.' zijn voor de bestandsnaamextensie.
* De lengte van de naam mag niet groter zijn dan 260 tekens.
* Er is een limiet voor de maximale bestandsgrootte die wordt ondersteund voor verwerking in Media Services. Raadpleeg [dit](media-services-quotas-and-limitations.md) artikel voor meer informatie over de maximale bestandsgrootte.
* Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). U moet dezelfde beleids-id gebruiken als u altijd dezelfde dagen/toegangsmachtigingen gebruikt, bijvoorbeeld beleidsregels voor locators die zijn bedoeld om gedurende een lange periode gehandhaafd te blijven (niet-upload-beleidsregels). Zie voor meer informatie [dit](media-services-dotnet-manage-entities.md#limit-access-policies) artikel.

Wanneer u assets maakt, u de volgende versleutelingsopties opgeven:

* **Geen**: er wordt geen versleuteling gebruikt. Dit is de standaardwaarde. Bij gebruik van deze optie is uw inhoud niet beveiligd tijdens het transport of in rust in opslag.
  Als u van plan bent een MP4 te leveren met behulp van progressieve download, gebruikt u deze optie: 
* **CommonEncryption** - Gebruik deze optie als u inhoud uploadt die al is versleuteld en beveiligd met Common Encryption of PlayReady DRM (bijvoorbeeld Smooth Streaming protected met PlayReady DRM).
* **EnvelopeEncrypted** – Gebruik deze optie als u HLS versleuteld uploadt met AES. Houd er rekening mee dat de bestanden moeten zijn gecodeerd en versleuteld door Transform Manager.
* **StorageEncrypted** - Versleutelt uw duidelijke inhoud lokaal met Behulp van AES-256-bits versleuteling en uploadt deze vervolgens naar Azure Storage, waar deze in rust versleuteld wordt opgeslagen. De versleuteling van assets die zijn beveiligd met Storage Encryption, wordt automatisch ongedaan gemaakt en de assets worden automatisch in een versleuteld bestandssysteem geplaatst voordat ze worden gecodeerd. Eventueel kunnen ze opnieuw worden versleuteld voordat ze opnieuw worden geüpload als een nieuwe uitvoerasset. Storage Encryption wordt voornamelijk gebruikt om uw invoerbestanden met media van hoge kwaliteit die zijn opgeslagen op de schijf, te beveiligen met een sterke versleuteling.
  
    Media Services biedt opslagversleuteling op de schijf voor uw assets, niet over-the-wire zoals Digital Rights Manager (DRM).
  
    Als de opslag van uw asset is versleuteld, moet u een beleid voor assetlevering configureren. Zie [Beleid voor het configureren van asset delivery voor](media-services-dotnet-configure-asset-delivery-policy.md)meer informatie .

Als u opgeeft dat uw asset moet worden versleuteld met een **CommonEncrypted-optie** of een **optie EnvelopeEncrypted,** moet u uw asset koppelen aan een **ContentKey.** Zie [Een ContentKey maken](media-services-dotnet-create-contentkey.md)voor meer informatie. 

Als u opgeeft dat uw asset moet worden versleuteld met een **optie StorageEncrypted,** maakt de Media Services SDK voor .NET een **StorageEncrypted** **ContentKey** voor uw asset.

In dit artikel ziet u hoe u Media Services .NET SDK en Media Services .NET SDK-extensies gebruiken om bestanden te uploaden naar een Media Services-asset.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Eén bestand uploaden met Media Services .NET SDK

De volgende code gebruikt .NET om één bestand te uploaden. De AccessPolicy en Locator worden gemaakt en vernietigd door de functie Uploaden. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Meerdere bestanden uploaden met Media Services .NET SDK
In de volgende code ziet u hoe u een asset maakt en meerdere bestanden uploadt.

De code doet het volgende:

* Hiermee maakt u een leeg element met de methode CreateEmptyAsset die in de vorige stap is gedefinieerd.
* Hiermee maakt u een **instantie AccessPolicy** die de machtigingen en de duur van de toegang tot het item definieert.
* Hiermee maakt u een **instantie Locator** die toegang biedt tot het item.
* Hiermee maakt u een **blobtransferclient-exemplaar.** Dit type vertegenwoordigt een client die werkt op de Azure-blobs. In dit voorbeeld houdt de client de voortgang van het uploaden in de gaten. 
* Somt door bestanden in de opgegeven map en maakt een **AssetFile-exemplaar** voor elk bestand.
* Uploadt de bestanden naar Media Services met behulp van de **Methode UploadAsync.** 

> [!NOTE]
> Gebruik de Methode UploadAsync om ervoor te zorgen dat de aanroepen niet worden geblokkeerd en dat de bestanden parallel worden geüpload.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Houd bij het uploaden van een groot aantal assets rekening met het volgende:

* Maak per thread een nieuw **CloudMediaContext-object.** De klasse **CloudMediaContext** is niet draadveilig.
* AantalOfConcurrentTransfers verhogen van de standaardwaarde van 2 naar een hogere waarde zoals 5. Het instellen van deze eigenschap is van invloed op alle exemplaren van **CloudMediaContext.** 
* Houd ParallelTransferThreadCount op de standaardwaarde van 10.

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Activa in bulk innemen met Media Services .NET SDK
Het uploaden van grote assetbestanden kan een knelpunt zijn tijdens het maken van activa. Het innemen van activa in bulk of "Bulk inname", houdt in dat het maken van activa wordt losgekoppeld van het uploadproces. Als u een bulkbenadering wilt gebruiken, maakt u een manifest (IngestManifest) waarin het actief en de bijbehorende bestanden worden beschreven. Gebruik vervolgens de uploadmethode van uw keuze om de bijbehorende bestanden te uploaden naar de blobcontainer van het manifest. Microsoft Azure Media Services houdt de blobcontainer in de gaten die aan het manifest is gekoppeld. Zodra een bestand is geüpload naar de blobcontainer, voltooit Microsoft Azure Media Services de assetcreatie op basis van de configuratie van het item in het manifest (IngestManifestAsset).

Als u een nieuw IngestManifest wilt maken, roept u de methode Maken aan die wordt weergegeven door de ingestmanifestenverzameling in de CloudMediaContext. Met deze methode wordt een nieuw IngestManifest gemaakt met de manifestnaam die u verstrekt.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Maak de elementen die zijn gekoppeld aan het bulk-inestmanifest. Configureer de gewenste versleutelingsopties op het item voor bulkinname.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

Een IngestManifestAsset associeert een asset met een bulk IngestManifest voor bulk inname. Het associeert ook de AssetFiles die deel uitmaakt van elk actief. Als u een IngestManifestAsset wilt maken, gebruikt u de methode Maken in de servercontext.

In het volgende voorbeeld wordt het toevoegen van twee nieuwe IngestManifestAssets getoond die de twee activa die eerder zijn gemaakt, koppelen aan het manifest voor de inname van bulk. Elke IngestManifestAsset associeert ook een set bestanden die worden geüpload voor elk item tijdens bulk inname.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

U elke snelle clienttoepassing gebruiken die de assetbestanden kan uploaden naar de blob-opslagcontainer URI die wordt geleverd door de eigenschap **IIngestManifest.BlobStorageUriForUpload** van het IngestManifest. 

In de volgende code ziet u hoe u .NET SDK gebruikt om de assets-bestanden te uploaden.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

De code voor het uploaden van de assetbestanden voor het voorbeeld dat in dit artikel wordt gebruikt, wordt weergegeven in het volgende codevoorbeeld:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

U de voortgang van de bulkinname voor alle activa die aan een **ingestmanifest** zijn gekoppeld, bepalen door de eigenschap Statistieken van het **IngestManifest te peilen.** Als u voortgangsgegevens wilt bijwerken, moet u elke keer dat u de eigenschap Statistieken peilt, een nieuwe **CloudMediaContext** gebruiken.

In het volgende voorbeeld wordt een innamemanifest door zijn **id**gepeild.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>Bestanden uploaden met .NET SDK-extensies
In het volgende voorbeeld ziet u hoe u één bestand uploadt met .NET SDK-extensies. In dit geval wordt de **methode CreateFromFile** gebruikt, maar is de asynchrone versie ook beschikbaar **(CreateFromFileAsync).** Met de methode **CreateFromFile** u de bestandsnaam, de versleutelingsoptie en een callback opgeven om de uploadvoortgang van het bestand te rapporteren.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

In het volgende voorbeeld wordt de functie UploadFile aangeroepen en wordt opslagversleuteling opgegeven als de optie voor het maken van activa.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Volgende stappen

U kunt nu de geüploade assets coderen. Zie [Assets coderen](media-services-portal-encode.md) voor meer informatie.

U kunt ook Azure Functions gebruiken om een coderingstaak te activeren op basis van een bestand dat binnenkomt in de geconfigureerde container. Zie [dit voorbeeld](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ) voor meer informatie.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Volgende stap
Nu u een asset hebt geüpload naar Media Services, gaat u naar het artikel [Hoe u een mediaprocessor krijgen.][How to Get a Media Processor]

[How to Get a Media Processor]: media-services-get-media-processor.md

