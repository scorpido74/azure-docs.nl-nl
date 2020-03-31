---
title: Gezicht en emotie detecteren met Azure Media Analytics | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u gezichten en emoties detecteren met Azure Media Analytics.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: 2d746167f993438e5fce467365844df2078c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919308"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Gezicht en emotie detecteren met Azure Media Analytics

> [!NOTE]
> De **mediaprocessor Azure Media Face Detector** wordt buiten gebruik gesteld. Zie voor de pensioendatum het onderwerp [oudere onderdelen.](legacy-components.md)

## <a name="overview"></a>Overzicht

Met **de Azure Media Face Detector-mediaprocessor** (MP) u bewegingen tellen, bijhouden en zelfs de deelname en reactie van het publiek meten via gezichtsuitdrukkingen. Deze service bevat twee functies: 

* **Gezichtsherkenning**
  
    Gezichtsherkenning vindt en volgt menselijke gezichten in een video. Meerdere gezichten kunnen worden gedetecteerd en vervolgens worden bijgehouden terwijl ze bewegen, waarbij de tijd- en locatiemetagegevens worden geretourneerd in een JSON-bestand. Tijdens het bijhouden probeert het een consistente ID aan hetzelfde gezicht te geven terwijl de persoon zich op het scherm beweegt, zelfs als ze worden belemmerd of kort het frame verlaten.
  
  > [!NOTE]
  > Deze dienst voert geen gezichtsherkenning uit. Een persoon die het frame verlaat of te lang wordt belemmerd, krijgt een nieuwe ID wanneer hij terugkeert.
  > 
  > 
* **Emotiedetectie**
  
    Emotion Detection is een optioneel onderdeel van de Face Detection Media Processor die de analyse van meerdere emotionele kenmerken van de gedetecteerde gezichten retourneert, waaronder geluk, verdriet, angst, woede en meer. 

De **Azure Media Face Detector** MP bevindt zich momenteel in Preview.

In dit artikel vindt u informatie over **Azure Media Face Detector** en wordt uitgelegd hoe u deze gebruiken met Media Services SDK voor .NET.

## <a name="face-detector-input-files"></a>Face Detector-invoerbestanden
Videobestanden. Momenteel worden de volgende indelingen ondersteund: MP4, MOV en WMV.

## <a name="face-detector-output-files"></a>Face Detector-uitvoerbestanden
De gezichtsherkenning en tracking API biedt hoge precisie gezichtsherkenning en tracking die tot 64 menselijke gezichten in een video kunnen detecteren. Frontale gezichten bieden de beste resultaten, terwijl zijvlakken en kleine gezichten (minder dan of gelijk aan 24x24 pixels) mogelijk niet zo nauwkeurig zijn.

De gedetecteerde en bijgehouden gezichten worden geretourneerd met coördinaten (links, boven, breedte en hoogte) die de locatie van gezichten in de afbeelding in pixels aangeven, evenals een face ID-nummer dat de tracking van die persoon aangeeft. Face ID-nummers zijn gevoelig voor reset onder omstandigheden wanneer het frontale gezicht verloren gaat of overlapt in het frame, wat resulteert in een aantal personen krijgen toegewezen meerdere ID's.

## <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Elementen van het JSON-bestand uitvoer

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Face Detector maakt gebruik van technieken van fragmentatie (waar de metadata kan worden opgesplitst in time-based brokken en je downloaden alleen wat je nodig hebt), en segmentatie (waar de gebeurtenissen worden opgesplitst in het geval ze te groot). Met enkele eenvoudige berekeningen kunt u de gegevens transformeren. Als een gebeurtenis bijvoorbeeld begon bij 6300 (teken), met een tijdschaal van 2997 (teken/sec) en framerate van 29,97 (frames/sec), dan:

* Start/tijdschaal = 2,1 seconde
* Seconden x Framerate = 63 frames

## <a name="face-detection-input-and-output-example"></a>Voorbeeld van invoer en uitvoer van gezichtsherkenning
### <a name="input-video"></a>Invoervideo
[Invoervideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Taakconfiguratie (vooraf ingesteld)
Wanneer u een taak maakt met **Azure Media Face Detector,** moet u een configuratievoorinstelling opgeven. De volgende configuratie-voorinstelling is alleen voor gezichtsherkenning.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Kenmerkenbeschrijvingen
| Kenmerknaam | Beschrijving |
| --- | --- |
| Modus |Snel - snelle verwerkingssnelheid, maar minder nauwkeurig (standaard).|

### <a name="json-output"></a>JSON-uitvoer
Het volgende voorbeeld van JSON-uitvoer is afgekapt.

```json
    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],
```


## <a name="emotion-detection-input-and-output-example"></a>Voorbeeld van emotiedetectie en output
### <a name="input-video"></a>Invoervideo
[Invoervideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Taakconfiguratie (vooraf ingesteld)
Wanneer u een taak maakt met **Azure Media Face Detector,** moet u een configuratievoorinstelling opgeven. De volgende configuratie-voorinstelling geeft aan json te maken op basis van de emotiedetectie.

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>Kenmerkenbeschrijvingen
| Kenmerknaam | Beschrijving |
| --- | --- |
| Modus |Gezichten: Alleen gezichtsherkenning.<br/>PerFaceEmotion: Breng emotie onafhankelijk terug voor elke gezichtsherkenning.<br/>AggregateEmotion: Retourneer gemiddelde emotiewaarden voor alle gezichten in frame. |
| AggregateEmotionWindowMs |Gebruik als de modus AggregateEmotion is geselecteerd. Hiermee geeft u de lengte op van video die wordt gebruikt om elk aggregaatresultaat in milliseconden te produceren. |
| AggregateEmotionIntervalMs |Gebruik als de modus AggregateEmotion is geselecteerd. Hiermee geeft u op met welke frequentie geaggregeerde resultaten moeten worden geproduceerd. |

#### <a name="aggregate-defaults"></a>Standaardwaarden voor aggregaat
Hieronder vindt u de aanbevolen waarden voor het totale venster en intervalinstellingen. AggregateEmotionWindowMs moeten langer zijn dan aggregateEmotionIntervalMs.

|| Standaardinstellingen(en) | Max(s) | Min(s) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0.5 |2 |0,25|
| AggregateEmotionIntervalMs |0.5 |1 |0,25|

### <a name="json-output"></a>JSON-uitvoer
JSON-uitvoer voor geaggregeerde emotie (afgekapt):

```json
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
```

## <a name="limitations"></a>Beperkingen
* De ondersteunde invoervideo-indelingen omvatten MP4, MOV en WMV.
* Het bereik van de detecteerbare gezichtsgrootte is 24x24 tot 2048x2048 pixels. De gezichten buiten dit bereik worden niet gedetecteerd.
* Voor elke video is het maximum aantal geretourneerde gezichten 64.
* Sommige gezichten worden mogelijk niet gedetecteerd als gevolg van technische problemen; bijvoorbeeld zeer grote gezichtshoeken (head-pose) en grote occlusie. Frontale en bijna frontale gezichten hebben de beste resultaten.

## <a name="net-sample-code"></a>.NET-voorbeeldcode

In het volgende programma ziet u hoe u:

1. Maak een asset en upload een mediabestand naar het item.
2. Maak een taak met een gezichtsherkenningstaak op basis van een configuratiebestand met de volgende json-voorinstelling: 

    ```json
            {
                "version": "1.0"
            }
    ```
3. Download de JSON-bestanden van uitvoer. 

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

namespace FaceDetection
{
    class Program
    {
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

            // Run the FaceDetection job.
            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\FaceDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
        }

        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Face Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Detection Job");

            // Get a reference to Azure Media Face Detector.
            string MediaProcessorName = "Azure Media Face Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Detection Output Asset", AssetCreationOptions.None);

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

[Azure Media Analytics-demo's](https://amslabs.azurewebsites.net/demos/Analytics.html)

