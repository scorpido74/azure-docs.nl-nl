---
title: Gezichten redacten met Azure Media Analytics | Microsoft Documenten
description: Azure Media Redactor is een Azure Media Analytics-mediaprocessor die schaalbare gezichtsredactie biedt in de cloud. In dit artikel wordt uitgelegd hoe u gezichten redleren met Azure-media-analyses.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6a1b7a76ef1efda51f09ac733b3d434235ff40ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900302"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Gezichten redacten met Azure Media Analytics 
## <a name="overview"></a>Overzicht
**Azure Media Redactor** is een [Azure Media Analytics](media-services-analytics-overview.md) mediaprocessor (MP) die schaalbare gezichtsredactie biedt in de cloud. Met gezichtsredactie u uw video wijzigen om gezichten van geselecteerde personen te vervagen. U de gezichtsredactieservice gebruiken in scenario's voor openbare veiligheid en nieuwsmedia. Een paar minuten beeldmateriaal dat meerdere gezichten bevat, kan uren duren om handmatig te redten, maar met deze service vereist het gezichtsredactieproces slechts een paar eenvoudige stappen. Voor meer informatie, zie [deze](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

In dit artikel vindt u informatie over **Azure Media Redactor** en wordt uitgelegd hoe u deze gebruiken met Media Services SDK voor .NET.

## <a name="face-redaction-modes"></a>De redactiemodi van het gezicht
Gezichtsredactie werkt door het detecteren van gezichten in elk frame van video en het bijhouden van het gezicht object zowel vooruit als achteruit in de tijd, zodat dezelfde persoon kan worden wazig uit andere hoeken ook. Het geautomatiseerde redactieproces is complex en produceert niet altijd 100% van de gewenste output, om deze reden biedt Media Analytics u een aantal manieren om de uiteindelijke uitvoer te wijzigen.

Naast een volledig automatische modus is er een twee-pass workflow, waarmee de selectie / de-selectie van gevonden gezichten via een lijst van id's. Ook, om willekeurige per frame aanpassingen van de MP maakt gebruik van een metadata bestand in JSON-indeling. Deze werkstroom is opgesplitst in de modi **Analyseren** en **Redact.** U de twee modi combineren in één pas waarin beide taken in één taak worden uitgevoerd. deze modus heet **Gecombineerd**.

### <a name="combined-mode"></a>Gecombineerde modus
Dit produceert een geredigeerde mp4 automatisch zonder handmatige invoer.

| Fase | Bestandsnaam | Opmerkingen |
| --- | --- | --- |
| Invoerasset |foo.bar |Video in WMV-, MOV- of MP4-indeling |
| Invoerconfig |Voorinstelling taakconfiguratie |{'version':'1.0', 'options': {'mode':'combined'}} |
| Uitvoeractief |foo_redacted.mp4 |Video met vervaging toegepast |

#### <a name="input-example"></a>Voorbeeld van invoer:
[bekijk deze video](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Voorbeeld van uitvoer:
[bekijk deze video](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Analysemodus
De **analysepass** van de twee-pas workflow neemt een video-ingang en produceert een JSON-bestand van gezichtslocaties, en jpg beelden van elk gedetecteerd gezicht.

| Fase | Bestandsnaam | Opmerkingen |
| --- | --- | --- |
| Invoerasset |foo.bar |Video in WMV-, MPV- of MP4-indeling |
| Invoerconfig |Voorinstelling taakconfiguratie |{'version':'1.0', 'options': {'mode':'analyze'}} |
| Uitvoeractief |foo_annotations.json |Annotatiegegevens van gezichtslocaties in JSON-indeling. Dit kan door de gebruiker worden bewerkt om de selectiekaders voor vervagende selectiete wijzigen. Zie hieronder het voorbeeld. |
| Uitvoeractief |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Een bijgesneden jpg van elk gedetecteerd gezicht, waarbij het getal het labelId van het gezicht aangeeft |

#### <a name="output-example"></a>Voorbeeld van uitvoer:

```json
    {
      "version": 1,
      "timescale": 24000,
      "offset": 0,
      "framerate": 23.976,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 48048,
          "interval": 1001,
          "events": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [
              {
                "index": 13,
                "id": 1138,
                "x": 0.29537,
                "y": -0.18987,
                "width": 0.36239,
                "height": 0.80335
              },
              {
                "index": 13,
                "id": 2028,
                "x": 0.60427,
                "y": 0.16098,
                "width": 0.26958,
                "height": 0.57943
              }
            ],

    … truncated
```

### <a name="redact-mode"></a>Redact-modus
De tweede pass van de workflow neemt een groter aantal ingangen die moeten worden gecombineerd tot een enkel element.

Dit omvat een lijst met id's om te vervagen, de originele video en de annotaties JSON. Deze modus gebruikt de annotaties om vervaging toe te passen op de invoervideo.

De uitvoer van de analysepas bevat niet de originele video. De video moet worden geüpload naar het invoerelement voor de taak van de Redact-modus en als het primaire bestand worden geselecteerd.

| Fase | Bestandsnaam | Opmerkingen |
| --- | --- | --- |
| Invoerasset |foo.bar |Video in WMV-, MPV- of MP4-indeling. Dezelfde video als in stap 1. |
| Invoerasset |foo_annotations.json |annotaties metadata bestand van fase een, met optionele wijzigingen. |
| Invoerasset |foo_IDList.txt (optioneel) |Optionele nieuwe lijn gescheiden lijst van gezicht ID's te redact. Als dit leeg blijft, vervaagt dit alle gezichten. |
| Invoerconfig |Voorinstelling taakconfiguratie |{'version':'1.0', 'options': {'mode':'redact'}} |
| Uitvoeractief |foo_redacted.mp4 |Video met vervaging toegepast op basis van annotaties |

#### <a name="example-output"></a>Voorbeelduitvoer
Dit is de uitvoer van een IDList waarbij één ID is geselecteerd.

[bekijk deze video](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Voorbeeld foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>Vervagingstypen

In de **modus Gecombineerd** of **Redact** zijn er 5 verschillende onscherptemodi waaruit u kiezen via de JSON-invoerconfiguratie: **Laag,** **Med,** **Hoog,** **Doos**en **Zwart**. Standaard wordt **Med** gebruikt.

Hieronder vindt u voorbeelden van de vervagende typen.

### <a name="example-json"></a>Voorbeeld JSON:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Laag

![Laag](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Hoog

![Hoog](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Zwart

![Zwart](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Elementen van het JSON-bestand uitvoer

De Redaction MP biedt hoge precisie gezichtsherkenning detectie en tracking die kan detecteren tot 64 menselijke gezichten in een videoframe. Frontale gezichten bieden de beste resultaten, terwijl zijvlakken en kleine gezichten (minder dan of gelijk aan 24x24 pixels) uitdagend zijn.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET-voorbeeldcode

In het volgende programma ziet u hoe u:

1. Maak een asset en upload een mediabestand naar het item.
2. Maak een taak met een taak voor gezichtsredactie op basis van een configuratiebestand met de volgende json-voorinstelling: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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

namespace FaceRedaction
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

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

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

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwante koppelingen
[Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md)

[Azure Media Analytics-demo's](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

