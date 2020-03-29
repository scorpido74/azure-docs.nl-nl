---
title: Tekst digitaliseren met Azure Media Analytics OCR | Microsoft Documenten
description: Met Azure Media Analytics OCR (optische tekenherkenning) u tekstinhoud in videobestanden converteren naar bewerkbare, doorzoekbare digitale tekst.  Hiermee u de extractie van zinvolle metadata van het videosignaal van uw media automatiseren.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 11889bd6df0bcc9564c17fdaacc333df1d418660
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918305"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Azure Media Analytics gebruiken om tekstinhoud in videobestanden om te zetten in digitale tekst  

> [!NOTE]
> De **Azure Media OCR-mediaprocessor** wordt buiten gebruik gesteld. Zie voor de pensioendatum het onderwerp [oudere onderdelen.](legacy-components.md)

## <a name="overview"></a>Overzicht
Als u tekstinhoud uit uw videobestanden moet extraheren en een bewerkbaar, doorzoekbare digitale tekst wilt genereren, moet u Azure Media Analytics OCR (optische tekenherkenning) gebruiken. Deze Azure Media Processor detecteert tekstinhoud in uw videobestanden en genereert tekstbestanden voor uw gebruik. OCR stelt u in staat om de extractie van zinvolle metadata van het videosignaal van uw media te automatiseren.

Wanneer u in combinatie met een zoekmachine wordt gebruikt, u uw media eenvoudig indexeren op tekst en de vindbaarheid van uw inhoud verbeteren. Dit is uiterst nuttig in zeer tekstuele video, zoals een video-opname of screen-capture van een diavoorstelling presentatie. De Azure OCR Media Processor is geoptimaliseerd voor digitale tekst.

De **Azure Media OCR-mediaprocessor** bevindt zich momenteel in Preview.

In dit artikel vindt u informatie over **Azure Media OCR** en wordt uitgelegd hoe u deze gebruiken met Media Services SDK voor .NET. Voor meer informatie en voorbeelden, zie [deze blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>OCR-invoerbestanden
Videobestanden. Momenteel worden de volgende indelingen ondersteund: MP4, MOV en WMV.

## <a name="task-configuration"></a>Configuratie van de taak
Taakconfiguratie (vooraf ingesteld). Wanneer u een taak maakt met **Azure Media OCR,** moet u een configuratievoorinstelling opgeven met JSON of XML. 

>[!NOTE]
>De OCR-engine neemt alleen een afbeeldingsgebied met minimaal 40 pixels tot maximaal 32000 pixels als geldige invoer in zowel hoogte/breedte.
>

### <a name="attribute-descriptions"></a>Kenmerkenbeschrijvingen
| Kenmerknaam | Beschrijving |
| --- | --- |
|Geavanceerde uitvoer| Als u AdvancedOutput op true instelt, bevat de JSON-uitvoer positionele gegevens voor elk woord (naast woordgroepen en regio's). Als u deze details niet wilt zien, stelt u de vlag in op false. De standaardwaarde is false. Voor meer informatie, zie [deze blog](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Taal |(optioneel) beschrijft de teksttaal om naar te kijken. Een van de volgende: AutoDetect (standaard), Arabisch, ChineesVereenvoudigd, ChineesTraditioneel, Deens, Nederlands, Engels, Fins, Frans, Duits, Grieks, Hongaars, Italiaans, Japans, Koreaans, Noors, Pools, Portugees, Roemeens, Russisch, ServischCyrillisch, Servisch Latijn, Slowaaks, Spaans, Zweeds, Turks. |
| Tekstoriëntatie |(optioneel) beschrijft de oriëntatie van tekst waarvoor u moet zoeken.  "Links" betekent dat de bovenkant van alle letters naar links gericht zijn.  Standaardtekst (zoals die welke in een boek te vinden is) kan "Up" georiënteerd worden genoemd.  Een van de volgende opties: Automatisch detecteren (standaard), Omhoog, Rechts, Omlaag, Links. |
| Tijdinterval |(facultatief) beschrijft de bemonsteringsfrequentie.  Standaard is elke 1/2 seconde.<br/>JSON-formaat – HH:mm:ss. SSS (standaard 00:00:00.500)<br/>XML-indeling – W3C XSD-duur primitief (standaard PT0.5) |
| Regio's detecteren |(facultatief) Een array met DetectRegion-objecten die gebieden in het videoframe opgeven om tekst te detecteren.<br/>Een object DetectRegion bestaat uit de volgende vier gehele getallen:<br/>Links – pixels van de linkermarge<br/>Boven – pixels vanaf de bovenste marge<br/>Breedte – breedte van het gebied in pixels<br/>Hoogte – hoogte van het gebied in pixels |

#### <a name="json-preset-example"></a>VOORBEELD JSON-voorinstelling

```json
    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }
```

#### <a name="xml-preset-example"></a>Voorbeeld van XML-voorinstelling

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""https://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""https://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""https://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>
```

## <a name="ocr-output-files"></a>OCR-uitvoerbestanden
De uitvoer van de OCR-mediaprocessor is een JSON-bestand.

### <a name="elements-of-the-output-json-file"></a>Elementen van het JSON-bestand uitvoer
De OCR-uitvoer met video biedt tijdsafhankelijke gegevens over de tekens in uw video.  U kenmerken zoals taal of oriëntatie gebruiken om precies de woorden aan te scherpen die u wilt analyseren. 

De uitvoer bevat de volgende kenmerken:

| Element | Beschrijving |
| --- | --- |
| Tijdschaal |"teken" per seconde van de video |
| Offset |tijdcompensatie voor tijdstempels. In versie 1.0 van Video API's is dit altijd 0. |
| Framesnelheid |Frames per seconde van de video |
| breedte |breedte van de video in pixels |
| hoogte |hoogte van de video in pixels |
| Fragmenten |array van tijdgebaseerde brokken video waarin de metagegevens zijn vergoten |
| start |begintijd van een fragment in "teken" |
| duur |lengte van een fragment in "teken" |
| interval |interval van elke gebeurtenis binnen het opgegeven fragment |
| events |array met regio's |
| regio |object dat gedetecteerde woorden of zinnen weergeeft |
| language |taal van de tekst die binnen een gebied wordt gedetecteerd |
| Oriëntatie |oriëntatie van de tekst die binnen een gebied wordt gedetecteerd |
| Lijnen |array van tekstregels die binnen een gebied worden gedetecteerd |
| tekst |de werkelijke tekst |

### <a name="json-output-example"></a>Voorbeeld van JSON-uitvoer
Het volgende uitvoervoorbeeld bevat de algemene video-informatie en verschillende videofragmenten. In elk videofragment bevat het elke regio, die wordt gedetecteerd door OCR MP met de taal en de tekstoriëntatie. De regio bevat ook alle woordregel in dit gebied met de tekst van de regel, de positie van de regel en alle woordinformatie (woordinhoud, positie en vertrouwen) in deze regel. Het volgende is een voorbeeld, en ik zet een aantal opmerkingen inline.

```json
    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }
```

## <a name="net-sample-code"></a>.NET-voorbeeldcode

In het volgende programma ziet u hoe u:

1. Maak een asset en upload een mediabestand naar het item.
2. Maak een taak met een OCR-configuratie/vooraf ingesteld bestand.
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

namespace OCR
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

            // Run the OCR job.
            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                        @"C:\supportFiles\OCR\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
        }

        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My OCR Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My OCR Job");

            // Get a reference to Azure Media OCR.
            string MediaProcessorName = "Azure Media OCR";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My OCR Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

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

