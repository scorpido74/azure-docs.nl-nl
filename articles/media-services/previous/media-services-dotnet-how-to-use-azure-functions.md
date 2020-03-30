---
title: Azure-functies ontwikkelen met Media Services
description: In dit onderwerp wordt uitgelegd hoe u Azure-functies ontwikkelen met Media Services met behulp van de Azure-portal.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 618acae10b874eb5ebd5b6da7fe081368528dbd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251165"
---
# <a name="develop-azure-functions-with-media-services"></a>Azure-functies ontwikkelen met Media Services

In dit artikel ziet u hoe u aan de slag met het maken van Azure-functies die Media Services gebruiken. De Azure-functie die in dit artikel is gedefinieerd, bewaakt een opslagaccountcontainer met de naam **invoer** voor nieuwe MP4-bestanden. Zodra een bestand in de opslagcontainer is gevallen, voert de blobtrigger de functie uit. Zie [Overzicht](../../azure-functions/functions-overview.md) en andere onderwerpen in de sectie **Azure-functies** voor het controleren van Azure-functies.

Als u bestaande Azure-functies wilt verkennen en implementeren die Azure Media Services gebruiken, raadpleegt u [Media Services Azure-functies](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Deze opslagplaats bevat voorbeelden die Media Services gebruiken om werkstromen weer te geven die betrekking hebben op het rechtstreeks opnemen van inhoud uit blobopslag, codering en het schrijven van inhoud naar blobopslag. Het bevat ook voorbeelden van het controleren van taakmeldingen via WebHooks en Azure Queues. U uw functies ook ontwikkelen op basis van de voorbeelden in de [Azure Functions-opslagplaats voor Media Services.](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) Als u de functies wilt implementeren, drukt u op de knop **Implementeren naar Azure.**

## <a name="prerequisites"></a>Vereisten

- Voordat u uw eerste functie kunt maken, moet u een actief Azure-account hebben. Als u nog geen Azure-account hebt, zijn er [gratis accounts beschikbaar](https://azure.microsoft.com/free/).
- Als u Azure-functies gaat maken die acties uitvoeren op uw Azure Media Services-account (AMS) of naar gebeurtenissen luisteren die door Media Services zijn verzonden, moet u een AMS-account maken, zoals [hier](media-services-portal-create-account.md)beschreven.
    
## <a name="create-a-function-app"></a>Een functie-app maken

1. Ga naar de [Azure-portal](https://portal.azure.com) en meld u aan met uw Azure-account.
2. Maak een functie-app zoals [hier](../../azure-functions/functions-create-function-app-portal.md)beschreven.

>[!NOTE]
> Een opslagaccount dat u opgeeft in de omgevingsvariabele **StorageConnection** (zie de volgende stap) moet zich in dezelfde regio bevinden als uw app.

## <a name="configure-function-app-settings"></a>Instellingen voor functie-apps configureren

Bij het ontwikkelen van Media Services-functies is het handig om omgevingsvariabelen toe te voegen die tijdens uw functies worden gebruikt. Als u app-instellingen wilt configureren, klikt u op de koppeling App-instellingen configureren. Zie [Instellingen voor Azure Function-apps configureren](../../azure-functions/functions-how-to-use-azure-function-app-settings.md)voor meer informatie. 

De functie, gedefinieerd in dit artikel, gaat ervan uit dat u de volgende omgevingsvariabelen in uw app-instellingen hebt:

**AMSAADTenantDomain**: Azure AD-tenanteindpunt. Zie [dit](media-services-use-aad-auth-to-access-ams-api.md) artikel voor meer informatie over verbinding maken met de AMS API.

**AMSRESTAPIEndpoint**: URI die het REST API-eindpunt vertegenwoordigt. 

**AMSClientId**: Azure AD-toepassingsclient-id.

**AMSClientSecret**: Azure AD-toepassingsclientgeheim.

**StorageConnection:** opslagverbinding van het account dat is gekoppeld aan het Media Services-account. Deze waarde wordt gebruikt in het **function.json-bestand** en **run.csx-bestand** (hieronder beschreven).

## <a name="create-a-function"></a>Een functie maken

Zodra uw functie-app is geïmplementeerd, u deze vinden tussen **Azure-functies van App Services.**

1. Selecteer de functie-app en klik op **Nieuwe functie**.
2. Kies het scenario **C#-taal** en **gegevensverwerking.**
3. Kies **blobtriggersjabloon.** Deze functie wordt geactiveerd wanneer een blob wordt geüpload naar de **invoercontainer.** De **invoernaam** wordt in de volgende stap opgegeven in **het pad.**

    ![bestanden](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Zodra u **BlobTrigger**hebt geselecteerd, worden er meer besturingselementen op de pagina weergegeven.

    ![bestanden](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Klik **op Maken**. 

## <a name="files"></a>Bestanden

Uw Azure-functie is gekoppeld aan codebestanden en andere bestanden die in deze sectie worden beschreven. Wanneer u de Azure-portal gebruikt om een functie te maken, worden **function.json** en **run.csx** voor u gemaakt. U moet een **project.json-bestand** toevoegen of uploaden. De rest van deze sectie geeft een korte uitleg van elk bestand en toont hun definities.

![bestanden](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Het bestand function.json definieert de functiebindingen en andere configuratie-instellingen. De runtime gebruikt dit bestand om de gebeurtenissen te bepalen die moeten worden gecontroleerd en hoe gegevens kunnen worden doorgegeven aan en deze gegevens van functieuitvoering kunnen worden doorgegeven. Zie [Azure-functies HTTP- en webhookbindingen](../../azure-functions/functions-reference.md#function-code)voor meer informatie.

>[!NOTE]
>Stel de **eigenschap disabled** in **op true** om te voorkomen dat de functie wordt uitgevoerd. 

Vervang de inhoud van het bestaande function.json-bestand door de volgende code:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

Het bestand project.json bevat afhankelijkheden. Hier vindt u een voorbeeld van **project.json-bestand** dat de vereiste .NET Azure Media Services-pakketten van Nuget bevat. Houd er rekening mee dat de versienummers veranderen met de nieuwste updates voor de pakketten, dus u moet de meest recente versies bevestigen. 

Voeg de volgende definitie toe aan project.json. 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>run.csx

Dit is de C#-code voor uw functie.  De functie die hieronder wordt gedefinieerd, bewaakt een opslagaccountcontainer met de naam **invoer** (dat is wat in het pad is opgegeven) voor nieuwe MP4-bestanden. Zodra een bestand in de opslagcontainer is gevallen, voert de blobtrigger de functie uit.
    
Het voorbeeld dat in deze sectie wordt gedefinieerd, toont aan dat 

1. hoe u een asset inneemt in een Media Services-account (door een blob om te zetten in een AMS-asset) en 
2. hoe u een coderingstaak indient die gebruikmaakt van de voorinstelling 'Adaptive Streaming' van Media Encoder Standard.

In het echte scenario wilt u waarschijnlijk de voortgang van de taak bijhouden en vervolgens uw gecodeerde asset publiceren. Zie [Azure WebHooks gebruiken om meldingen van mediaservices te controleren](media-services-dotnet-check-job-progress-with-webhooks.md)voor meer informatie. Zie Media Services [Azure-functies](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)voor meer voorbeelden .  

Vervang de inhoud van het bestaande run.csx-bestand door de volgende code: Zodra u klaar bent met het definiëren van uw functie, klikt u op **Opslaan en uitvoeren**.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>Uw functie testen

Als u uw functie wilt testen, moet u een MP4-bestand uploaden naar de **invoercontainer** van het opslagaccount dat u in de verbindingstekenreeks hebt opgegeven.  

1. Selecteer het opslagaccount dat u hebt opgegeven in de omgevingsvariabele **StorageConnection.**
2. Klik **op Blobs**.
3. Klik op **+ Container**. Geef de **containerinvoer een**naam .
4. Druk op **Uploaden** en blader naar een .mp4-bestand dat u wilt uploaden.

>[!NOTE]
> Wanneer u een blobtrigger gebruikt op een verbruiksplan, kan er tot 10 minuten vertraging optreden bij het verwerken van nieuwe blobs nadat een functie-app niet actief is geweest. Nadat de functie-app is uitgevoerd, worden blobs onmiddellijk verwerkt. Zie [Blob-opslagtriggers en -bindingen](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Op dit moment bent u klaar om te beginnen met het ontwikkelen van een Media Services applicatie. 
 
Zie het voorbeeld van het voorbeeld van de integratie van [Media Services .NET-functies op GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) voor meer informatie en volledige voorbeelden/oplossingen voor het gebruik van Azure-functies en logische apps met Azure Media Services voor het maken van aangepaste werkstromen voor het maken van inhoud.

Zie Azure [WebHooks gebruiken om functiemeldingen van Media Services te controleren met .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

