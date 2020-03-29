---
title: Azure Media Video Miniaturen gebruiken om een videosoming te maken | Microsoft Documenten
description: Videosummarisatie kan u helpen bij het maken van samenvattingen van lange video's door automatisch interessante fragmenten uit de bronvideo te selecteren. Dit is handig als u een snel overzicht wilt geven van wat u verwachten in een lange video.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: a79e718c04f81b1552d63ab98b6dcd6bb428fb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918311"
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Azure Media Video Miniaturen gebruiken om een videosoming te maken  

> [!NOTE]
> De **mediaprocessor Azure Media Video Thumbnails** wordt buiten gebruik gesteld. Zie voor de pensioendatum het onderwerp [oudere onderdelen.](legacy-components.md)

## <a name="overview"></a>Overzicht

Met **de mediaprocessor Miniaturen** van Azure Media Video (MP) u een samenvatting maken van een video die handig is voor klanten die alleen een voorbeeld van een samenvatting van een lange video willen bekijken. Klanten willen bijvoorbeeld een korte 'overzichtsvideo' zien wanneer ze over een miniatuur zweven. Door de parameters van **Azure Media Video Miniaturen** aan te passen via een configuratie-voorinstelling, u de krachtige opnamedetectie- en samenvoegingstechnologie van de MP gebruiken om algoritmisch een beschrijvende subclip te genereren.  

Het **MP voor miniatuur van Azure Media Video** bevindt zich momenteel in Preview.

In dit artikel vindt u informatie over **azure mediavideominiatuur** en wordt uitgelegd hoe u deze gebruiken met Media Services SDK voor .NET.

## <a name="limitations"></a>Beperkingen

In sommige gevallen, als uw video niet uit verschillende scènes bestaat, zal de uitvoer slechts één schot zijn.

## <a name="video-summary-example"></a>Voorbeeld van videooverzicht
Hier volgen enkele voorbeelden van wat de mediaprocessor Azure Media Video Thumbnails kan doen:

### <a name="original-video"></a>Originele video
[Originele video](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Resultaat voor videominiatuur
[Resultaat voor videominiatuur](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>Taakconfiguratie (vooraf ingesteld)
Wanneer u een miniatuurtaak voor video's maakt met **Azure Media Video Miniaturen,** moet u een configuratievoorinstelling opgeven. Het bovenstaande miniatuurvoorbeeld is gemaakt met de volgende basis-JSON-configuratie:

```json
    {
        "version":"1.0"
    }
```

Momenteel u de volgende parameters wijzigen:

| Param | Beschrijving |
| --- | --- |
| uitvoerAudio |Hiermee geeft u op of de resulterende video audio bevat. <br/>Toegestane waarden zijn: Waar of Onwaar. Standaard is True. |
| fadeInFadeOut fadeout |Hiermee geeft u op of overgangen met vervagen al dan niet worden gebruikt tussen de afzonderlijke bewegingsminiaturen.  <br/>Toegestane waarden zijn: Waar of Onwaar.  Standaard is True. |
| maxMotionThumbnailDurationInSecs |Geheel getal dat aangeeft hoe lang de volledige resulterende video moet zijn.  Standaard is afhankelijk van de oorspronkelijke videoduur. |

In de volgende tabel wordt de standaardduur beschreven wanneer **maxMotionThumbnailInSecs** niet wordt gebruikt.

|  |  |  |
| --- | --- | --- |
| Videoduur |d < 3 min |3 min < d < 15 min |
| Miniatuurduur |15 sec (2-3 scènes) |30 sec (3-5 scènes) |

De volgende JSON stelt beschikbare parameters in.

```json
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }
```

## <a name="net-sample-code"></a>.NET-voorbeeldcode

In het volgende programma ziet u hoe u:

1. Maak een asset en upload een mediabestand naar het item.
2. Hiermee maakt u een taak met een miniatuurtaak voor video op basis van een configuratiebestand met de volgende json-voorinstelling: 
    
    ```json
            {                
                "version": "1.0",
                "options": {
                    "outputAudio": "true",
                    "maxMotionThumbnailDurationInSecs": "30",
                    "fadeInFadeOut": "false"
                }
            }
    ```

3. Downloadt de uitvoerbestanden. 

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

    namespace VideoSummarization
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


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

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

### <a name="video-thumbnail-output"></a>Videominiatuuruitvoer
[Videominiatuuruitvoer](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwante koppelingen
[Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md)

[Azure Media Analytics-demo's](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

