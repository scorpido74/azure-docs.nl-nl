---
title: Tekst met Azure Media Analytics OCR | Microsoft Docs
description: Met Azure Media Analytics OCR (optische teken herkenning) kunt u tekst inhoud in video bestanden converteren naar bewerkbaar, Doorzoek bare digitale tekst.  Zo kunt u het uitpakken van zinvolle meta gegevens van het video signaal van uw media automatiseren.
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
ms.openlocfilehash: 11f897852ce820e666d7403f42735b2ee3bdd73b
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084826"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Azure Media Analytics gebruiken om tekst inhoud in video bestanden te converteren naar digitale tekst  

## <a name="overview"></a>Overzicht
Als u tekst inhoud uit uw video bestanden wilt extra heren en een bewerkbaar, Doorzoek bare digitale tekst wilt genereren, moet u Azure Media Analytics OCR gebruiken (optische teken herkenning). Deze Azure media-processor detecteert tekst inhoud in uw video bestanden en genereert tekst bestanden voor uw gebruik. Met OCR kunt u het uitpakken van zinvolle meta gegevens van het video signaal van uw media automatiseren.

Wanneer u gebruikt in combi natie met een zoek machine, kunt u uw media eenvoudig indexeren op tekst en de vind baarheid van uw inhoud verbeteren. Dit is zeer nuttig in video met hoge tekst, zoals een video-opname of een scherm opname van een diapresentatie. De Azure OCR-media processor is geoptimaliseerd voor digitale tekst.

De OCR-media processor van **Azure media** is momenteel beschikbaar als preview-versie.

Dit artikel bevat informatie over de **OCR van Azure media** en laat zien hoe u deze kunt gebruiken met Media Services SDK voor .net. Zie [deze blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/)voor meer informatie en voor beelden.

## <a name="ocr-input-files"></a>OCR-invoer bestanden
Video bestanden. Momenteel worden de volgende indelingen ondersteund: MP4, MOV en WMV.

## <a name="task-configuration"></a>Configuratie van de taak
Taak configuratie (voor instelling). Wanneer u een taak met **Azure media OCR**maakt, moet u een configuratie definitie opgeven met behulp van JSON of XML. 

>[!NOTE]
>De OCR-engine gebruikt alleen een afbeeldings regio met mini maal 40 pixels tot Maxi maal 32000 pixels als geldige invoer in de hoogte/breedte.
>

### <a name="attribute-descriptions"></a>Kenmerk beschrijvingen
| Kenmerk naam | Beschrijving |
| --- | --- |
|AdvancedOutput| Als u AdvancedOutput instelt op True, bevat de JSON-uitvoer positionele gegevens voor elk enkel woord (naast zinsdelen en regio's). Als u deze gegevens niet wilt weer geven, stelt u de vlag in op ONWAAR. De standaardwaarde is false. Zie [deze blog](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/)voor meer informatie.|
| Taal |(optioneel) Hiermee wordt de taal van de tekst beschreven waarnaar moet worden gezocht. Een van de volgende: automatische detectie (standaard), Arabisch, ChineseSimplified, ChineseTraditional, Tsjechisch, Nederlands, Engels, Fins, Frans, Duits, Grieks, Hong aars, Italiaans, Japans, Koreaans, Noors, Pools, Portugees, Roemeens, Russisch, SerbianCyrillic, SerbianLatin, Slowaaks, Spaans, Zweeds en Turks. |
| TextOrientation |(optioneel) Hiermee wordt de richting van de tekst beschreven die moet worden gezocht.  "Links" betekent dat de bovenkant van alle letters naar links wordt gewijsd.  Standaard tekst (zoals die kan worden gevonden in een boek) kan worden aangeroepen.  Een van de volgende: automatisch detecteren (standaard), omhoog, rechts, omlaag, links. |
| TimeInterval |(optioneel) Hiermee wordt de sampling frequentie beschreven.  De standaard waarde is elke 1/2 seconden.<br/>JSON-indeling – uu: mm: SS. SSS (standaard 00:00:00.500)<br/>XML-indeling-primitieve W3C-duur van de XSD (standaard PT 0,5) |
| DetectRegions |Beschrijving Een matrix met DetectRegion-objecten die regio's binnen het video frame opgeven waarin tekst moet worden gedetecteerd.<br/>Een DetectRegion-object wordt gemaakt van de volgende vier gehele waarden:<br/>Links: pixels vanaf de linkermarge<br/>Boven – pixels van de bovenste marge<br/>Breedte: breedte van de regio in pixels<br/>Hoogte: hoogte van de regio in pixels |

#### <a name="json-preset-example"></a>Voor beeld van JSON-voor instelling

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

#### <a name="xml-preset-example"></a>Voor beeld van XML-voor instelling

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

## <a name="ocr-output-files"></a>OCR-uitvoer bestanden
De uitvoer van de OCR-media processor is een JSON-bestand.

### <a name="elements-of-the-output-json-file"></a>Elementen van het JSON-uitvoer bestand
De OCR-uitvoer van de video biedt tijdgebonden gegevens over de tekens die in uw video worden gevonden.  U kunt kenmerken zoals taal of richting gebruiken om in te stellen op exact de woorden die u wilt analyseren. 

De uitvoer bevat de volgende kenmerken:

| Element | Beschrijving |
| --- | --- |
| Tijdschaal |' Ticks ' per seconde van de video |
| Offset |tijd verschuiving voor tijds tempels. In versie 1,0 van video-Api's is dit altijd 0. |
| Framesnelheid |Frames per seconde van de video |
| Breedte |de breedte van de video in pixels |
| hoogte |de hoogte van de video in pixels |
| Fragmenten |matrix van op tijd gebaseerde segmenten van video waarin de meta gegevens worden gesegmenteerd |
| start |begin tijd van een fragment in ' Ticks ' |
| duration |lengte van een fragment in ' Ticks ' |
| interval |interval van elke gebeurtenis binnen het gegeven fragment |
| events |matrix met regio's |
| regio |object dat gedetecteerde woorden of zinsdelen voor stelt |
| language |taal van de tekst die in een regio is gedetecteerd |
| opstelling |de stand van de gedetecteerde tekst binnen een regio |
| transferorderregels |matrix met tekst regels die binnen een regio zijn gedetecteerd |
| tekst |de werkelijke tekst |

### <a name="json-output-example"></a>Voor beeld van JSON-uitvoer
In het volgende voor beeld van de uitvoer vindt u de algemene video-informatie en verschillende video fragmenten. In elk video fragment bevat deze elke regio, die wordt gedetecteerd door OCR-MP met de taal en de tekst richting. De regio bevat ook elke woord regel in deze regio met de tekst van de regel, de positie van de lijn en alle informatie over het woord (inhoud, positie en betrouw baarheid van Word) op deze regel. Hier volgt een voor beeld en ik heb een aantal opmerkingen inline geplaatst.

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

## <a name="net-sample-code"></a>.NET-voorbeeld code

Het volgende programma laat zien hoe u:

1. Maak een Asset en upload een media bestand naar de Asset.
2. Maak een taak met een OCR-configuratie/vooraf ingesteld bestand.
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

## <a name="related-links"></a>Gerelateerde koppelingen
[Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md)

