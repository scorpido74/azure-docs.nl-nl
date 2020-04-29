---
title: Bewegingen detecteren met Azure Media Analytics | Microsoft Docs
description: Met de Azure Media Motion Detector media processor (MP) kunt u op efficiënte wijze gedeelten van interesses identificeren binnen een andere, lange en niet-gebeurtenisloze video.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: f4c021531a4d04bf16e5dbee4172952433f675d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77913001"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Bewegingen detecteren met Azure Media Analytics

> [!NOTE]
> De **Azure Media Motion detector** -media processor wordt buiten gebruik gesteld. Zie het onderwerp [oudere onderdelen](legacy-components.md) voor de datum van beëindiging.
 
## <a name="overview"></a>Overzicht

Met de **Azure Media Motion detector** media processor (MP) kunt u op efficiënte wijze gedeelten van interesses identificeren binnen een andere, lange en niet-gebeurtenisloze video. Bewegings detectie kan worden gebruikt voor statische camera beelden om secties van de video te identificeren waar beweging optreedt. Er wordt een JSON-bestand gegenereerd met een meta gegevens met tijds tempels en het grens gebied waarin de gebeurtenis plaatsvond.

Deze technologie is gericht op beveiligings videofeeds en kan bewegingen in relevante gebeurtenissen categoriseren en fout-positieven, zoals scha duwen en belichtings veranderingen. Op deze manier kunt u beveiligings waarschuwingen genereren op basis van camera feeds zonder spam met eindeloze irrelevante gebeurtenissen, terwijl u de momenten van belang stelling van lange bewakings Video's kunt ophalen.

De **Azure Media Motion detector** -MP is momenteel beschikbaar als preview-versie.

Dit artikel bevat informatie over **Azure Media Motion detector** en laat zien hoe u deze kunt gebruiken met Media Services SDK voor .net

## <a name="motion-detector-input-files"></a>Bewegings detector-invoer bestanden
Video bestanden. Momenteel worden de volgende indelingen ondersteund: MP4, MOV en WMV.

## <a name="task-configuration-preset"></a>Taak configuratie (voor instelling)
Wanneer u een taak met **Azure Media Motion detector**maakt, moet u een voor instelling voor de configuratie opgeven. 

### <a name="parameters"></a>Parameters
U kunt de volgende para meters gebruiken:

| Naam | Opties | Beschrijving | Standaard |
| --- | --- | --- | --- |
| sensitivityLevel |Teken reeks: laag, gemiddeld, hoog |Hiermee stelt u het gevoeligheids niveau in waarmee bewegingen worden gerapporteerd. Stel dit in op het aanpassen van het aantal fout-positieven. |drager |
| frameSamplingValue |Positief geheel getal |Hiermee stelt u de frequentie in waarmee algoritmen worden uitgevoerd. 1 is gelijk aan elk frame, 2 betekent elk tweede frame, enzovoort. |1 |
| detectLightChange |Boolean: ' True ', ' false ' |Hiermee wordt ingesteld of lichte wijzigingen worden gerapporteerd in de resultaten |Terecht |
| mergeTimeThreshold |XS-time: uu: mm: SS<br/>Voor beeld: 00:00:03 |Hiermee geeft u het tijd venster op tussen bewegings gebeurtenissen waarbij twee gebeurtenissen worden gecombineerd en gerapporteerd als 1. |00:00:00 |
| detectionZones |Een matrix met detectie zones:<br/>-De detectie zone is een matrix van drie of meer punten<br/>-Punt is een x-en y-coördinaat van 0 tot 1. |Hiermee wordt de lijst met veelhoek detectie zones beschreven die moeten worden gebruikt.<br/>Resultaten worden gerapporteerd met de zones als een ID, waarbij de eerste een id is: 0 |Eén zone, die het hele frame bedekt. |

### <a name="json-example"></a>JSON-voor beeld

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>Uitvoer bestanden voor Motion detector
Een bewegings detectie taak retourneert een JSON-bestand in het uitvoer element, waarin de animatie waarschuwingen en de bijbehorende categorieën binnen de video worden beschreven. Het bestand bevat informatie over de tijd en duur van de beweging die in de video is gedetecteerd.

De bewegings detector-API biedt indica toren wanneer er objecten in beweging zijn in een video met een vaste achtergrond (bijvoorbeeld een bewakings video). De bewegings detector is getraind om te voor komen dat er valse waarschuwingen, zoals belichtings-en schaduw wijzigingen, worden verminderd. De huidige beperkingen van de algoritmen zijn onder andere nacht visie Video's, semi transparante objecten en kleine objecten.

### <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Elementen van het JSON-uitvoer bestand
> [!NOTE]
> In de meest recente versie is de uitvoer JSON-indeling gewijzigd en kan dit een belang rijke wijziging voor sommige klanten vormen.
> 
> 

In de volgende tabel worden elementen van het JSON-uitvoer bestand beschreven.

| Element | Beschrijving |
| --- | --- |
| versie |Dit verwijst naar de versie van de video-API. De huidige versie is 2. |
| lijnen |' Ticks ' per seconde van de video. |
| offset |De tijds verschuiving voor tijds tempels in Ticks. In versie 1,0 van video-Api's is dit altijd 0. Deze waarde kan worden gewijzigd in toekomstige scenario's die worden ondersteund. |
| snelheid |Aantal frames per seconde video. |
| breedte, hoogte |Verwijst naar de breedte en hoogte van de video in pixels. |
| start |Het tijds tempel van de begin datum in Ticks. |
| duur |De lengte van de gebeurtenis, in Ticks. |
| interval |Het interval van elke vermelding in de gebeurtenis, in Ticks. |
| events |Elk gebeurtenis fragment bevat de beweging die binnen die tijds duur is gedetecteerd. |
| type |In de huidige versie is dit altijd ' 2 ' voor algemene beweging. Dit label biedt video-Api's de flexibiliteit voor het categoriseren van bewegingen in toekomstige versies. |
| regionId |Zoals hierboven is uitgelegd, is dit altijd 0 in deze versie. Dit label geeft video-API de flexibiliteit om beweging in verschillende regio's in toekomstige versies te vinden. |
| secties |Verwijst naar het gebied in uw video waar u aandacht besteedt aan beweging. <br/><br/>-' id ' vertegenwoordigt het gebied regio – in deze versie is er slechts één, ID 0. <br/>-"type" vertegenwoordigt de vorm van de regio die u voor de beweging bevalt. Op dit moment worden "Rectangle" en "veelhoek" ondersteund.<br/> Als u ' Rectangle ' hebt opgegeven, heeft de regio dimensies in X, Y, width en height. De X-en Y-coördinaten vertegenwoordigen de linkerbovenhoek van de linker XY-coördinaten van de regio in een genormaliseerde schaal van 0,0 tot 1,0. De breedte en hoogte staan voor de grootte van de regio in een genormaliseerde schaal van 0,0 tot 1,0. In de huidige versie worden X, Y, width en height altijd vastgesteld op 0, 0 en 1, 1. <br/>Als u ' veelhoek ' hebt opgegeven, heeft de regio dimensies in punten. <br/> |
| fragmenten |De meta gegevens worden gesegmenteerd in verschillende segmenten die fragmenten worden genoemd. Elk fragment bevat een start, een duur, een intervalnummer en een of meer gebeurtenissen. Een fragment zonder gebeurtenissen houdt in dat er geen bewegingen zijn gedetecteerd tijdens de start tijd en duur. |
| vier Kante haken [] |Elk haakje vertegenwoordigt één interval in de gebeurtenis. Lege haken voor dat interval betekent dat er geen beweging is gedetecteerd. |
| locaties |In deze nieuwe vermelding onder gebeurtenissen wordt de locatie vermeld waarop de beweging heeft plaatsgevonden. Dit is specifieker dan de detectie zones. |

In het volgende JSON-voor beeld ziet u de uitvoer:

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>Beperkingen
* De ondersteunde indelingen voor video-invoer zijn MP4, MOV en WMV.
* Bewegings detectie is geoptimaliseerd voor stationaire achtergrond Video's. Het algoritme is gericht op het beperken van valse waarschuwingen, zoals belichtings wijzigingen en scha duwen.
* Sommige bewegingen worden mogelijk niet gedetecteerd als gevolg van technische problemen; bijvoorbeeld: een video met nacht visie, semi transparante objecten en kleine objecten.

## <a name="net-sample-code"></a>.NET-voorbeeld code

Het volgende programma laat zien hoe u:

1. Maak een Asset en upload een media bestand naar de Asset.
2. Maak een taak met een video bewegings detectie taak op basis van een configuratie bestand dat de volgende JSON-voor instelling bevat: 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
            }
            }
    ```

3. Down load de JSON-uitvoer bestanden. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkel omgeving in en vul in het bestand app. config de verbindings informatie in, zoals beschreven in [Media Services ontwikkeling met .net](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Voorbeeld

```csharp

using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace VideoMotionDetection
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

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detection Output Asset", AssetCreationOptions.None);

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
[Azure Media Services Motion detector-blog](https://azure.microsoft.com/blog/motion-detector-update/)

[Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md)

[Demo's Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

