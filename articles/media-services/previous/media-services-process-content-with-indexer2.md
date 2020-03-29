---
title: Mediabestanden indexeren met Azure Media Indexer 2 Preview | Microsoft Documenten
description: Azure Media Indexer stelt u in staat om inhoud van uw mediabestanden doorzoekbaar te maken en een volledige teksttranscriptie te genereren voor ondertiteling en trefwoorden. In dit onderwerp wordt uitgelegd hoe u Media Indexer 2 Preview gebruiken.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: adsolank
ms.openlocfilehash: c24218dc116803ca0e0a1f166b7b54b24fc4d5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163791"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Mediabestanden indexeren met Azure Media Indexer 2 Preview

> [!NOTE]
> De **mediaprocessor Azure Media Indexer 2** wordt buiten gebruik gesteld. Zie voor de pensioendata dit [onderwerp met oudere onderdelen.](legacy-components.md) [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) vervangt deze verouderde mediaprocessor. Zie [Migreren van Azure Media Indexer en Azure Media Indexer 2 naar Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)voor meer informatie.

Met de **Azure Media Indexer 2** Preview-mediaprocessor (MP) u mediabestanden en inhoud doorzoekbaar maken en ondertitelingstracks genereren. In vergelijking met de vorige versie van [Azure Media Indexer](media-services-index-content.md)presteert **Azure Media Indexer 2 Preview** sneller indexering en biedt het bredere taalondersteuning. Ondersteunde talen zijn Engels, Spaans, Frans, Duits, Italiaans, Chinees (Mandarijn, Vereenvoudigd), Portugees, Arabisch, Russisch en Japans.

Het **MP voor de proefversie van Azure Media Indexer 2** bevindt zich momenteel in Preview.

In dit artikel ziet u hoe u indexeringstaken maakt met **Azure Media Indexer 2 Preview**.

## <a name="considerations"></a>Overwegingen

De volgende overwegingen zijn van toepassing:
 
* Indexer 2 wordt niet ondersteund in Azure China 21Vianet en Azure Government.
* Bij het indexeren van inhoud, zorg ervoor dat mediabestanden die zeer duidelijke spraak hebben (zonder achtergrondmuziek, ruis, effecten, of microfoon sissen) te gebruiken. Enkele voorbeelden van de juiste inhoud zijn: opgenomen vergaderingen, lezingen of presentaties. De volgende inhoud is mogelijk niet geschikt voor indexering: films, tv-programma's, alles met gemengde audio- en geluidseffecten, slecht opgenomen inhoud met achtergrondgeluid (gesis).
 
## <a name="input-and-output-files"></a>Invoer- en uitvoerbestanden
### <a name="input-files"></a>Invoerbestanden
Audio- of videobestanden

### <a name="output-files"></a>Uitvoerbestanden
Een indexeringstaak kan bestanden met ondertiteling genereren in de volgende indelingen:  

* **TTML TTML**
* **WebvTT**

Cc-bestanden (Closed Caption) in deze indelingen kunnen worden gebruikt om audio- en videobestanden toegankelijk te maken voor mensen met een gehoorhandicap.

## <a name="task-configuration-preset"></a>Taakconfiguratie (vooraf ingesteld)
Wanneer u een indexeringstaak maakt met **Azure Media Indexer 2 Preview,** moet u een configuratievoorinstelling opgeven.

De volgende JSON stelt beschikbare parameters in.

```json
    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }
```

## <a name="supported-languages"></a>Ondersteunde talen
Azure Media Indexer 2 Preview ondersteunt spraak-naar-tekst voor de volgende talen (wanneer u de taalnaam in de taakconfiguratie opgeeft, gebruikt u 4-tekencode tussen haakjes zoals hieronder wordt weergegeven):

* Engels [Enus]
* Spaans [Eses]
* Chinees (Mandarijn, Vereenvoudigd) [ZhCn]
* Frans [FrFr]
* Duits [Dede]
* Italiaans [Itit]
* Portugees [PtBr]
* Arabisch (Egyptisch) [Areg]
* Japans [JaJp]
* Russisch [RuRu]
* Brits Engels [EnGb]
* Spaans (Mexico) [EsMx] 

## <a name="supported-file-types"></a>Ondersteunde bestandstypen

Zie de sectie [ondersteunde codecs/indelingen](media-services-media-encoder-standard-formats.md#input-containerfile-formats) voor informatie over ondersteunde bestandstypen.

## <a name="net-sample-code"></a>.NET-voorbeeldcode

In het volgende programma ziet u hoe u:

1. Maak een asset en upload een mediabestand naar het item.
2. Maak een taak met een indexeringstaak op basis van een configuratiebestand met de volgende json-voorinstelling:

    ```json
            {
            "version":"1.0",
            "Features":
                [
                {
                "Options": {
                        "Formats":["WebVtt","ttml"],
                        "Language":"enUs",
                        "Type":"RecoOptions"
                },
                "Type":"SpReco"
                }]
            }
    ```
    
3. Download de uitvoerbestanden. 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkelomgeving in en vul het app.config-bestand in met verbindingsgegevens, zoals beschreven in [de ontwikkeling van Media Services met .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Voorbeeld

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace IndexContent
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run indexing job.
            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                        @"C:\supportFiles\Indexer\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
        }

        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Indexing Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Indexing Job");

            // Get a reference to Azure Media Indexer 2 Preview.
            string MediaProcessorName = "Azure Media Indexer 2 Preview";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Indexing Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be indexed.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

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
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
                .Where(p => p.Name == mediaProcessorName)
                .ToList()
                .OrderBy(p => new Version(p.Version))
                .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                                           mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwante koppelingen
[Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md)

[Azure Media Analytics-demo's](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

