---
title: Video-inhoud analyseren op aanstootgevend materiaal in C# - Content Moderator
titleSuffix: Azure Cognitive Services
description: Video-inhoud analyseren voor verschillende aanstootgevende materialen met behulp van de Content Moderator SDK voor .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 71858755fe31823d4d7ef8623b915db851530116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72755241"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Video-inhoud analyseren op aanstootgevend materiaal in C #

In dit artikel vindt u informatie en codevoorbeelden waarmee u aan de slag met de [Content Moderator SDK voor .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) om video-inhoud te scannen op inhoud voor volwassenen of racy.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. 

## <a name="prerequisites"></a>Vereisten
- Een versie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Azure-resources instellen

De videomoderatiemogelijkheid van de inhoudsmoderator is beschikbaar als een gratis **mediaprocessor** voor openbare preview in Azure Media Services (AMS). Azure Media Services is een gespecialiseerde Azure-service voor het opslaan en streamen van video-inhoud. 

### <a name="create-an-azure-media-services-account"></a>Een Azure Media Services-account maken

Volg de instructies in [Een Azure Media Services-account maken](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) om u te abonneren op AMS en een bijbehorend Azure-opslagaccount te maken. Maak in dat opslagaccount een nieuwe Blob-opslagcontainer.

### <a name="create-an-azure-active-directory-application"></a>Een Azure Active Directory-toepassing maken

Navigeer naar uw nieuwe AMS-abonnement in de Azure-portal en selecteer **API-toegang** in het zijmenu. Selecteer **Verbinding maken met Azure Media Services met serviceprincipal**. Let op de waarde in het **veld PUNT REST API;** dit later nodig hebt.

Selecteer in de sectie **Azure AD-app** de optie **Nieuw maken** en uw nieuwe Azure AD-toepassingsregistratie een naam geven (bijvoorbeeld 'VideoModADApp'). Klik **op Opslaan** en wacht een paar minuten terwijl de toepassing is geconfigureerd. Vervolgens ziet u uw nieuwe app-registratie onder het gedeelte **Azure AD-app** van de pagina.

Selecteer uw app-registratie en klik op de knop **Toepassing beheren** eronder. Let op de waarde in het veld **Toepassings-id;** dit later nodig hebt. Selecteer **Instellingentoetsen** > **Keys**en voer een beschrijving in voor een nieuwe sleutel (zoals 'VideoModKey'). Klik **op Opslaan**en let vervolgens op de nieuwe sleutelwaarde. Kopieer deze string en bewaar het ergens veilig.

Zie Aan de slag met Azure [AD-verificatie](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad)voor een grondiger doorloop van het bovenstaande proces.

Zodra u dit hebt gedaan, u de mediaprocessor voor videobeheer op twee verschillende manieren gebruiken.

## <a name="use-azure-media-services-explorer"></a>Azure Media Services Explorer gebruiken

De Azure Media Services Explorer is een gebruiksvriendelijke frontend voor AMS. Gebruik het om door je AMS-account te bladeren, video's te uploaden en inhoud te scannen met de mediaprocessor van Content Moderator. Download en installeer het vanaf [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases)of bekijk het blogpost van [Azure Media Services Explorer](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) voor meer informatie.

![Explorer azure Media Services met inhoudsmoderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Het Visual Studio-project maken

1. Maak in Visual Studio een nieuw **console-appproject (.NET Framework)** en noem het **VideoModeration**. 
1. Als uw oplossing nog meer projecten bevat, selecteert u deze als het enkele opstartproject.
1. Download de vereiste NuGet-pakketten. Klik met de rechtermuisknop op uw project in Solution Explorer en selecteer **NuGet-pakketten beheren**. Zoek en installeer vervolgens de volgende pakketten:
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>Videomoderatiecode toevoegen

Vervolgens kopieert en plakt u de code uit deze handleiding in uw project om een eenvoudig scenario voor inhoudstoezicht te implementeren.

### <a name="update-the-programs-using-statements"></a>De using-instructies van het programma bijwerken

Voeg aan het begin van het bestand _Program.cs_ de volgende `using`-instructies toe.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>Resourceverwijzingen instellen

Voeg de volgende statische velden toe aan de klasse **Programma** in _Program.cs_. In deze velden zijn de gegevens bevatten die nodig zijn om verbinding te maken met uw AMS-abonnement. Vul ze in met de waarden die je hebt in de bovenstaande stappen. Houd `CLIENT_ID` er rekening mee dat dit de `CLIENT_SECRET` waarde van de **toepassings-id** van uw Azure AD-app is en de waarde is van de 'VideoModKey' die u voor die app hebt gemaakt.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Als u een lokaal videobestand (eenvoudigste aanvraag) wilt gebruiken, voegt `INPUT_FILE` u het toe aan het project en voert u het pad in als de waarde (relatieve paden zijn relatief ten opzichte van de uitvoeringsmap).

U moet ook het _bestand preset.json_ maken in de huidige map en het gebruiken om een versienummer op te geven. Bijvoorbeeld:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>De invoervideo(s) laden

De **hoofdmethode** van de klasse **Program** maakt een Azure Media Context en vervolgens een Azure Storage Context (voor het geval uw video's zich in blobopslag bevinden). De resterende code scant een video vanuit een lokale map, blob of meerdere blobs in een Azure-opslagcontainer. U alle opties uitproberen door de andere regels code uit te delen.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Een Azure Media-context maken

Voeg de volgende methode toe aan de klasse **Programma.** Dit maakt gebruik van uw AMS-referenties om communicatie met AMS mogelijk te maken.

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>De code toevoegen om een Azure-opslagcontext te maken

Voeg de volgende methode toe aan de klasse **Programma.** U gebruikt de opslagcontext, die is gemaakt op uw opslagreferenties, om toegang te krijgen tot uw blob-opslag.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>De code toevoegen om Azure Media-elementen te maken van lokaal bestand en blob

De mediaprocessor Van Content Moderator voert taken uit op **Assets** binnen het Azure Media Services-platform.
Met deze methoden worden de elementen gemaakt uit een lokaal bestand of een bijbehorende blob.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>De code toevoegen om een verzameling video's (als blobs) in een container te scannen

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>De methode toevoegen om de taak inhoudsmoderator uit te voeren

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>Helperfuncties toevoegen

Met deze methoden wordt het inhoudsmoderatoruitvoerbestand (JSON) gedownload van het Azure Media Services-item en wordt de status van de moderatietaak bijgehouden, zodat het programma een hardloopstatus op de console kan registreren.

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>Het programma uitvoeren en de uitvoer controleren

Nadat de taak Inhoudsmatiging is voltooid, analyseert u de JSON-respons. Het bestaat uit deze elementen:

- Video-informatieoverzicht
- **Shots** als "**fragmenten**"
- **Hoofdframes** als **"gebeurtenissen**" met een **reviewAanbevolen" (= waar of onwaar)"** vlag op basis **van adult** en **racy** scores
- **begin,** **duur**, **totaalDuur**en **tijdstempel** zijn in "teken". Delen door **de tijdschaal** om het nummer in seconden te krijgen.
 
> [!NOTE]
> - `adultScore`vertegenwoordigt de potentiële aanwezigheid sein- en voorspellingsscore van inhoud die in bepaalde situaties als seksueel expliciet of volwassen kan worden beschouwd.
> - `racyScore`vertegenwoordigt de potentiële aanwezigheid en voorspelling score van de inhoud die kan worden beschouwd als seksueel suggestief of volwassen in bepaalde situaties.
> - `adultScore`en `racyScore` zijn tussen 0 en 1. Hoe hoger de score, hoe hoger het model voorspelt dat de categorie van toepassing kan zijn. Deze preview is gebaseerd op een statistisch model in plaats van handmatig gecodeerde resultaten. We raden u aan te testen met uw eigen inhoud om te bepalen hoe elke categorie aansluit bij uw vereisten.
> - `reviewRecommended`is waar of onwaar, afhankelijk van de interne scoredrempels. Klanten moeten beoordelen of ze deze waarde moeten gebruiken of aangepaste drempels moeten bepalen op basis van hun inhoudsbeleid.

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het genereren van [videobeoordelingen](video-reviews-quickstart-dotnet.md) vanuit je moderatie-uitvoer.

Voeg [transcriptmoderatie](video-transcript-moderation-review-tutorial-dotnet.md) toe aan je videorecensies.

Bekijk de gedetailleerde tutorial over het bouwen van een [complete video- en transcriptiemoderatieoplossing.](video-transcript-moderation-review-tutorial-dotnet.md)

[Download de Visual Studio-oplossing](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) voor deze en andere Content Moderator quickstarts voor .NET.
