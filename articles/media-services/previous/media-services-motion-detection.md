---
title: Bewegingen detecteren met Azure Media Analytics | Microsoft Documenten
description: Met de Azure Media Motion Detector-mediaprocessor (MP) u delen van belang efficiënt identificeren binnen een anders lange en rustige video.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913001"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Bewegingen detecteren met Azure Media Analytics

> [!NOTE]
> De **mediaprocessor Azure Media Motion Detector** wordt buiten gebruik gesteld. Zie voor de pensioendatum het onderwerp [oudere onderdelen.](legacy-components.md)
 
## <a name="overview"></a>Overzicht

Met **de Azure Media Motion Detector-mediaprocessor** (MP) u delen van belang efficiënt identificeren binnen een anders lange en rustige video. Bewegingsdetectie kan worden gebruikt op statische camerabeelden om delen van de video te identificeren waar beweging plaatsvindt. Het genereert een JSON-bestand met een metagegevens met tijdstempels en het grensgebied waar de gebeurtenis heeft plaatsgevonden.

Gericht op beveiligingsvideofeeds, is deze technologie in staat om beweging te categoriseren in relevante gebeurtenissen en false positives zoals schaduwen en lichtveranderingen. Hiermee u beveiligingswaarschuwingen genereren van camerafeeds zonder te worden gespamd met eindeloze irrelevante gebeurtenissen, terwijl u momenten van interesse extraheren uit lange bewakingsvideo's.

De **Azure Media Motion Detector** MP bevindt zich momenteel in Preview.

In dit artikel vindt u informatie over **Azure Media Motion Detector** en wordt uitgelegd hoe u deze gebruiken met Media Services SDK voor .NET

## <a name="motion-detector-input-files"></a>Invoerbestanden bewegingsdetector
Videobestanden. Momenteel worden de volgende indelingen ondersteund: MP4, MOV en WMV.

## <a name="task-configuration-preset"></a>Taakconfiguratie (vooraf ingesteld)
Wanneer u een taak maakt met **Azure Media Motion Detector,** moet u een configuratievoorinstelling opgeven. 

### <a name="parameters"></a>Parameters
U de volgende parameters gebruiken:

| Name | Opties | Beschrijving | Standaard |
| --- | --- | --- | --- |
| gevoeligheidNiveau |String:'laag', 'medium', 'hoog' |Hiermee stelt u het gevoeligheidsniveau in waarop bewegingen worden gerapporteerd. Pas dit aan om het aantal fout-positieven aan te passen. |'medium' |
| frameSampling-waarde |Positief geheel getal |Hiermee stelt u de frequentie in waarop het algoritme wordt uitgevoerd. 1 is gelijk aan elk frame, 2 betekent elk tweede frame, enzovoort. |1 |
| detectLightChange |Boolean:'waar', 'vals' |Hiermee bepaalt u of lichtwijzigingen worden gerapporteerd in de resultaten |'Vals' |
| mergeTimeThreshold |Xs-tijd: Hh:mm:ss<br/>Voorbeeld: 00:00:03 |Hiermee geeft u het tijdvenster op tussen bewegingsgebeurtenissen waarbij twee gebeurtenissen worden gecombineerd en gerapporteerd als 1. |00:00:00 |
| detectiezones |Een scala aan detectiezones:<br/>- Detectiezone is een array van 3 of meer punten<br/>- Punt is een x en y coördinaat van 0 tot 1. |Beschrijft de lijst met veelhoekige detectiezones die moeten worden gebruikt.<br/>Resultaten worden gerapporteerd met de zones als een ID, met de eerste is 'id':0 |Enkele zone, die het hele frame bedekt. |

### <a name="json-example"></a>Json voorbeeld

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

## <a name="motion-detector-output-files"></a>Bewegingsdetectoruitvoerbestanden
Een functie voor bewegingsdetectie retourneert een JSON-bestand in het uitvoerelement, waarin de bewegingswaarschuwingen en de categorieën ervan in de video worden beschreven. Het bestand bevat informatie over de tijd en duur van de bewegingstijd die in de video wordt gedetecteerd.

De Motion Detector API geeft indicatoren zodra er objecten in beweging zijn in een vaste achtergrondvideo (bijvoorbeeld een bewakingsvideo). De Bewegingsdetector is getraind om vals alarm te verminderen, zoals verlichting en schaduwwisselingen. De huidige beperkingen van de algoritmen omvatten nachtzichtvideo's, semi-transparante objecten en kleine objecten.

### <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Elementen van het JSON-bestand uitvoer
> [!NOTE]
> In de nieuwste versie is de INDELING Uitvoer JSON gewijzigd en kan dit voor sommige klanten een baanbrekende wijziging betekenen.
> 
> 

In de volgende tabel worden elementen van het JSON-bestand voor uitvoer beschreven.

| Element | Beschrijving |
| --- | --- |
| versie |Dit verwijst naar de versie van de Video API. De huidige versie is 2. |
| Tijdschaal |"Teken" per seconde van de video. |
| offset |De tijdscompensatie voor tijdstempels in 'teken'. In versie 1.0 van Video API's is dit altijd 0. In toekomstige scenario's die we ondersteunen, kan deze waarde veranderen. |
| Framerate |Aantal frames per seconde video. |
| breedte, hoogte |Verwijst naar de breedte en hoogte van de video in pixels. |
| start |De starttijdstempel in "teken". |
| duur |De lengte van het evenement, in "teken". |
| interval |Het interval van elk item in het evenement, in "teken". |
| events |Elk gebeurtenisfragment bevat de beweging die binnen die tijdsduur wordt gedetecteerd. |
| type |In de huidige versie is dit altijd '2' voor generieke beweging. Dit label geeft Video API's de flexibiliteit om beweging in toekomstige versies te categoriseren. |
| regioId |Zoals hierboven uitgelegd, zal dit altijd 0 in deze versie. Dit label geeft Video API de flexibiliteit om beweging te vinden in verschillende regio's in toekomstige versies. |
| Regio 's |Verwijst naar het gebied in uw video waar u de zorg over beweging. <br/><br/>-"id" vertegenwoordigt het regiogebied – in deze versie is er slechts één, ID 0. <br/>-"type" vertegenwoordigt de vorm van de regio waar u om geeft voor beweging. Momenteel worden 'rechthoek' en 'veelhoek' ondersteund.<br/> Als u 'rechthoek' hebt opgegeven, heeft het gebied afmetingen in X, Y, Breedte en Hoogte. De X- en Y-coördinaten vertegenwoordigen de xy-coördinaten van de regio in een genormaliseerde schaal van 0,0 tot 1,0. De breedte en hoogte vertegenwoordigen de grootte van het gebied in een genormaliseerde schaal van 0,0 tot 1,0. In de huidige versie worden X, Y, Width en Height altijd vastgesteld op 0, 0 en 1, 1. <br/>Als u 'veelhoek' hebt opgegeven, heeft het gebied afmetingen in punten. <br/> |
| Fragmenten |De metagegevens worden samengevoegd in verschillende segmenten, fragmenten genaamd. Elk fragment bevat een start, een duur, een intervalnummer en een of meer gebeurtenissen. Een fragment zonder gebeurtenissen betekent dat er geen beweging is gedetecteerd tijdens die begintijd en duur. |
| haakjes [] |Elke beugel vertegenwoordigt één interval in de gebeurtenis. Lege haakjes voor dat interval betekent dat er geen beweging is gedetecteerd. |
| locaties |In deze nieuwe vermelding onder gebeurtenissen wordt de locatie weergegeven waar de beweging is uitgevoerd. Dit is specifieker dan de detectiezones. |

In het volgende JSON-voorbeeld wordt de uitvoer weergegeven:

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
* De ondersteunde invoervideo-indelingen omvatten MP4, MOV en WMV.
* Bewegingsdetectie is geoptimaliseerd voor stilstaande achtergrondvideo's. Het algoritme richt zich op het verminderen van vals alarm, zoals lichtveranderingen en schaduwen.
* Sommige bewegingen kunnen niet worden gedetecteerd als gevolg van technische uitdagingen; bijvoorbeeld nachtzichtvideo's, semi-transparante objecten en kleine objecten.

## <a name="net-sample-code"></a>.NET-voorbeeldcode

In het volgende programma ziet u hoe u:

1. Maak een asset en upload een mediabestand naar het item.
2. Maak een taak met een videobewegingsdetectietaak op basis van een configuratiebestand met de volgende json-voorinstelling: 
   
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
[Azure Media Services-bewegingsdetectorblog](https://azure.microsoft.com/blog/motion-detector-update/)

[Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md)

[Azure Media Analytics-demo's](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

