---
title: Media bestanden indexeren met Azure Media Indexer 2 Preview | Microsoft Docs
description: Met Azure Media Indexer kunt u inhoud van uw media bestanden doorzoekbaar maken en een transcriptie van volledige tekst genereren voor ondertiteling en tref woorden. In dit onderwerp wordt beschreven hoe u Media Indexer 2-Preview gebruikt.
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
ms.openlocfilehash: d03c3a15d9bccf93b73d36302f986dffd95c6428
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309253"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Media bestanden indexeren met Azure Media Indexer 2 Preview

> [!NOTE]
> De [Azure media indexer 2](media-services-process-content-with-indexer2.md) media processor wordt buiten gebruik gesteld op 1 januari van 2020. [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) vervangt deze verouderde media processor. Zie [Migrate from Azure media indexer en Azure media indexer 2 to Azure Media Services video indexer](migrate-indexer-v1-v2.md)voor meer informatie.

Met de **Azure media indexer 2 Preview** media processor (MP) kunt u media bestanden en inhoud doorzoekbaar maken, evenals ondertitelings tracks genereren. Vergeleken met de vorige versie van [Azure media indexer](media-services-index-content.md), heeft **Azure media indexer 2-Preview** snellere indexering en biedt het uitgebreidere taal ondersteuning. Ondersteunde talen zijn Engels, Spaans, Frans, Duits, Italiaans, Chinees (Mandarijn, vereenvoudigd), Portugees, Arabisch, Russisch en Japans.

De **Azure media indexer 2 Preview** -MP is momenteel beschikbaar als preview-versie.

In dit artikel wordt beschreven hoe u Indexeer taken maakt met **Azure media indexer 2-Preview**.

## <a name="considerations"></a>Overwegingen

De volgende overwegingen zijn van toepassing:
 
* Indexeer functie 2 wordt niet ondersteund in azure China 21Vianet en Azure Government.
* Wanneer u inhoud indexeert, moet u ervoor zorgen dat u media bestanden gebruikt met zeer duidelijke spraak (zonder achtergrond muziek, ruis, effecten of microfoon hiss). Enkele voor beelden van de juiste inhoud zijn: vastgelegde vergaderingen, colleges of presentaties. De volgende inhoud is mogelijk niet geschikt voor indexering: films, TV-Program ma's, alles met gemengde audio en geluids effecten, slecht vastgelegde inhoud met achtergrond geluid (hiss).
 
## <a name="input-and-output-files"></a>Invoer-en uitvoer bestanden
### <a name="input-files"></a>Invoer bestanden
Audio-of video bestanden

### <a name="output-files"></a>Uitvoerbestanden
Een indexerings taak kan ondertitelings bestanden genereren in de volgende indelingen:  

* **SAMI**
* **TTML**
* **WebVTT**

Ondertitelings bestanden (CC) in deze indelingen kunnen worden gebruikt om audio-en video bestanden toegankelijk te maken voor mensen met een gehoor handicap.

## <a name="task-configuration-preset"></a>Taak configuratie (voor instelling)
Bij het maken van een indexerings taak met **Azure media indexer 2 Preview**moet u een configuratie voorinstelling opgeven.

De volgende JSON-sets beschik bare para meters.

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
Azure Media Indexer 2 Preview ondersteunt spraak naar tekst voor de volgende talen (wanneer u de taal naam opgeeft in de taak configuratie, gebruikt u 4-teken code tussen vier Kante haken zoals hieronder wordt weer gegeven):

* Engels [EnUs]
* Spaans [EsEs]
* Chinees (Mandarijn, vereenvoudigd) [ZhCn]
* Frans [FrFr]
* Duits [DeDe]
* Italiaans [ItIt]
* Portugees [PtBr]
* Arabisch (Egyptisch) [ArEg]
* Japans [JaJp]
* Russisch [RuRu]
* Brits Engels [EnGb]
* Spaans (Mexico) [EsMx] 

## <a name="supported-file-types"></a>Ondersteunde bestands typen

Zie de sectie [ondersteunde codecs/indelingen](media-services-media-encoder-standard-formats.md#input-containerfile-formats) voor meer informatie over ondersteunde bestands typen.

## <a name="net-sample-code"></a>.NET-voorbeeld code

Het volgende programma laat zien hoe u:

1. Maak een Asset en upload een media bestand naar de Asset.
2. Maak een taak met een indexerings taak op basis van een configuratie bestand dat de volgende JSON-voor instelling bevat:

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
    
3. Down load de uitvoer bestanden. 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkelomgeving in en vul in het bestand app.config de verbindingsinformatie in, zoals beschreven in [Media Services ontwikkelen met .NET](media-services-dotnet-how-to-use.md). 

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

[Demo's Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

