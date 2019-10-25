---
title: Gezichts-en Emotion detecteren met Azure Media Analytics | Microsoft Docs
description: In dit onderwerp ziet u hoe gezichten en emoties met Azure Media Analytics worden gedetecteerd.
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
ms.openlocfilehash: 6fa2495ee580bb8e74a0d026533fa90f20743510
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881770"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Gezichts-en Emotion detecteren met Azure Media Analytics

> [!IMPORTANT]
> Bekijk de [pensioen plannen](media-services-analytics-overview.md#retirement-plans) van sommige media processors.

## <a name="overview"></a>Overzicht
Met de **Azure media face detector** media processor (MP) kunt u het aantal bewegingen en de inschakeling van de publieks-en reactie activiteiten via gezichts uitdrukkingen tellen, bijhouden en meten. Deze service bevat twee functies: 

* **Gezichts detectie**
  
    Met gezichts detectie wordt geconstateerd en getraceerd voor menselijke gezichten in een video. Er kunnen meerdere gezichten worden gedetecteerd en vervolgens worden gevolgd wanneer ze worden verplaatst, met de tijd en locatie-meta gegevens die zijn geretourneerd in een JSON-bestand. Tijdens het bijhouden van wordt geprobeerd een consistente ID te geven aan hetzelfde gezicht, terwijl de persoon op het scherm gaat, zelfs als ze niet meer of korter zijn dan het frame.
  
  > [!NOTE]
  > Deze service voert geen gezichts herkenning uit. Een persoon die het frame verlaat of gedurende te lang wordt belemmerd, krijgt een nieuwe ID wanneer deze wordt geretourneerd.
  > 
  > 
* **Emotion-detectie**
  
    Emotion Detection is een optioneel onderdeel van de Gezichtsdetectie media processor dat analyse op meerdere emotioneel-kenmerken van de gedetecteerde gezichten retourneert, waaronder blij, verdriet, vrezen, boosheid en meer. 

De **Azure media face detector** -MP is momenteel beschikbaar als preview-versie.

Dit artikel bevat informatie over **Azure media face detector** en laat zien hoe u deze kunt gebruiken met Media Services SDK voor .net.

## <a name="face-detector-input-files"></a>Gezichts detector-invoer bestanden
Video bestanden. Momenteel worden de volgende indelingen ondersteund: MP4, MOV en WMV.

## <a name="face-detector-output-files"></a>Face-uitvoer bestanden voor detector
De gezichts detectie-en tracerings-API biedt een hoge precisie voor detectie en tracering van locaties die in een video Maxi maal 64 menselijke gezichten kunnen detecteren. Front-gezichten bieden de beste resultaten, terwijl de zijde gezichten en kleine gezichten (kleiner dan of gelijk aan 24x24 pixels) mogelijk niet zo nauw keurig zijn.

De gedetecteerde en getraceerde gezichten worden geretourneerd met coördinaten (links, boven, breedte en hoogte) die de locatie van de gezichten in de afbeelding in pixels aangeven, evenals een face ID-nummer dat het bijhouden van die persoon aangeeft. Gezichts-ID-nummers zijn gevoelig voor het opnieuw instellen van de voor kant wanneer het frontale gezicht verloren is gegaan of wordt overlapt in het kader, wat betekent dat sommige personen meerdere Id's krijgen toegewezen.

## <a id="output_elements"></a>Elementen van het JSON-uitvoer bestand

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Face detector gebruikt technieken voor fragmentatie (waarbij de meta gegevens kunnen worden opgesplitst in op tijd gebaseerde segmenten en u kunt alleen downloaden wat u nodig hebt), en segmentatie (waarbij de gebeurtenissen worden opgesplitst als ze te groot zijn). Met enkele eenvoudige berekeningen kunt u de gegevens transformeren. Als bijvoorbeeld een gebeurtenis is gestart om 6300 (Ticks), met een tijd schaal van 2997 (Ticks/sec) en de frame snelheid van 29,97 (frames/sec), dan:

* Start/tijdschaal = 2,1 seconde
* Seconden x frame snelheid = 63 frames

## <a name="face-detection-input-and-output-example"></a>Voor beeld van de invoer en uitvoer van gezichts detectie
### <a name="input-video"></a>Video invoeren
[Video invoeren](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Taak configuratie (voor instelling)
Wanneer u een taak met **Azure media face detector**maakt, moet u een voor instelling voor de configuratie opgeven. De volgende configuratie voorinstelling is alleen bedoeld voor gezichts detectie.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Kenmerk beschrijvingen
| Kenmerk naam | Beschrijving |
| --- | --- |
| Modus |Snelle verwerkings snelheid, maar minder nauw keurig (standaard).|

### <a name="json-output"></a>JSON-uitvoer
Het volgende voor beeld van de JSON-uitvoer is afgekapt.

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


## <a name="emotion-detection-input-and-output-example"></a>Voor beeld van de invoer en uitvoer van de Emotion-detectie
### <a name="input-video"></a>Video invoeren
[Video invoeren](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Taak configuratie (voor instelling)
Wanneer u een taak met **Azure media face detector**maakt, moet u een voor instelling voor de configuratie opgeven. Met de volgende configuratie vooraf kunt u JSON maken op basis van de detectie van de Emotion.

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


#### <a name="attribute-descriptions"></a>Kenmerk beschrijvingen
| Kenmerk naam | Beschrijving |
| --- | --- |
| Modus |Gezichten: alleen gezichts detectie.<br/>PerFaceEmotion: retourneert Emotion onafhankelijk voor elke gezichts detectie.<br/>AggregateEmotion: gemiddelde Emotion-waarden retour neren voor alle gezichten in het kader. |
| AggregateEmotionWindowMs |Gebruiken als de AggregateEmotion-modus is geselecteerd. Hiermee geeft u de lengte van de video op die wordt gebruikt voor het produceren van elk samengeteld resultaat, in milliseconden. |
| AggregateEmotionIntervalMs |Gebruiken als de AggregateEmotion-modus is geselecteerd. Hiermee geeft u op met welke frequentie statistische resultaten moeten worden geproduceerd. |

#### <a name="aggregate-defaults"></a>Cumulatieve standaard waarden
Hieronder vindt u de aanbevolen waarden voor het aggregatie venster en de interval instellingen. AggregateEmotionWindowMs moet langer zijn dan AggregateEmotionIntervalMs.

|| Standaard waarden (s) | Max. (n) | Min (en) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0,25|
| AggregateEmotionIntervalMs |0,5 |1 |0,25|

### <a name="json-output"></a>JSON-uitvoer
JSON-uitvoer voor cumulatieve Emotion (afgekapt):

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
* De ondersteunde indelingen voor video-invoer zijn MP4, MOV en WMV.
* Het bereik van de gedetecteerde gezichts grootte is 24x24 in 2048x2048 pixels. De gezichten buiten dit bereik worden niet gedetecteerd.
* Het maximum aantal geretourneerde gezichten voor elke video is 64.
* Sommige gezichten worden mogelijk niet gedetecteerd als gevolg van technische problemen; bijvoorbeeld zeer grote gezichts hoeken (hoofd pose) en grote bedekking. Front-en bijna-frontale gezichten hebben de beste resultaten.

## <a name="net-sample-code"></a>.NET-voorbeeld code

Het volgende programma laat zien hoe u:

1. Maak een Asset en upload een media bestand naar de Asset.
2. Maak een taak met een gezichts detectie taak op basis van een configuratie bestand dat de volgende JSON-voor instelling bevat: 

    ```json
            {
                "version": "1.0"
            }
    ```
3. Down load de JSON-uitvoer bestanden. 

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

## <a name="related-links"></a>Gerelateerde koppelingen
[Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md)

[Demo's Azure Media Analytics](https://amslabs.azurewebsites.net/demos/Analytics.html)

