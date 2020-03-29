---
title: Miniatuurweergaven genereren met Media Encoder Standard met .NET
description: In dit onderwerp wordt uitgelegd hoe u .NET gebruiken om een asset te coderen en tegelijkertijd miniaturen te genereren met Behulp van Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bc29c098bcf7ef1d1a2e2532a00c95f0ec7e927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61244226"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Miniatuurweergaven genereren met Media Encoder Standard met .NET 

U Media Encoder Standard gebruiken om een of meer miniaturen te genereren uit uw invoervideo in [JPEG-,](https://en.wikipedia.org/wiki/JPEG) [PNG-](https://en.wikipedia.org/wiki/Portable_Network_Graphics)of BMP-afbeeldingsbestandsindelingen. [BMP](https://en.wikipedia.org/wiki/BMP_file_format) U taken indienen die alleen afbeeldingen produceren, of u het genereren van miniaturen combineren met codering. In dit artikel vindt u een aantal voorbeeld-XML- en JSON-miniatuurvoorinstellingen voor dergelijke scenario's. Aan het einde van het artikel is er een [voorbeeldcode](#code_sample) die laat zien hoe u de Media Services .NET SDK gebruiken om de coderingstaak te volbrengen.

Voor meer informatie over de elementen die worden gebruikt in voorbeeldvoorinstellingen, moet u [het schema van Media Encoder Standard bekijken.](media-services-mes-schema.md)

Controleer de sectie [Overwegingen.](media-services-dotnet-generate-thumbnail-with-mes.md#considerations)
    
## <a name="example-of-a-single-png-file-preset"></a>Voorbeeld van een voorinstelling 'enkel PNG-bestand'

De volgende JSON- en XML-voorinstelling kan worden gebruikt om één PNG-uitvoerbestand te produceren vanaf de eerste paar seconden van de invoervideo, waarbij de encoder een best-effort poging doet om een "interessant" frame te vinden. Houd er rekening mee dat de afmetingen van de uitvoerafbeelding zijn ingesteld op 100%, wat betekent dat deze overeenkomen met de afmetingen van de invoervideo. Let ook op hoe de instelling 'Opmaak' in 'Uitvoer' nodig is om het gebruik van "PngLayers" in de sectie "Codecs" te evenaren. 

### <a name="json-preset"></a>JSON-voorinstelling

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
```
    
### <a name="xml-preset"></a>XML-voorinstelling

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Voorbeeld van een voorinstelling 'reeks JPEG-afbeeldingen'

De volgende JSON- en XML-voorinstelling kan worden gebruikt om een set van 10 afbeeldingen te produceren bij tijdstempels van 5%, 15%, ..., 95% van de invoertijdlijn, waarbij de afbeeldingsgrootte wordt opgegeven als een kwart van die van de invoervideo.

### <a name="json-preset"></a>JSON-voorinstelling

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-voorinstelling
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Voorbeeld van een voorinstelling 'één afbeelding op een specifieke tijdstempel'

De volgende JSON- en XML-voorinstelling kan worden gebruikt om één JPEG-afbeelding te produceren op het merk van 30 seconden van de invoervideo. Deze voorinstelling verwacht dat de invoervideo meer dan 30 seconden duurt (anders mislukt de taak).

### <a name="json-preset"></a>JSON-voorinstelling

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-voorinstelling
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Voorbeeld van een voorinstelling 'miniaturen met verschillende resoluties'

De volgende voorinstelling kan worden gebruikt om miniaturen te genereren met verschillende resoluties in één taak. In het voorbeeld, op posities 5%, 15%, ..., 95% van de input tijdlijn, de encoder genereert twee beelden - een op 100% van de input video resolutie en de andere op 50%.

Let op het gebruik van de macro {Resolution} in de FileName; het geeft aan de encoder aan om de breedte en hoogte te gebruiken die u hebt opgegeven in de sectie Codering van de voorinstelling terwijl u de bestandsnaam van de uitvoerafbeeldingen genereert. Dit helpt u ook onderscheid te maken tussen de verschillende beelden gemakkelijk

### <a name="json-preset"></a>JSON-voorinstelling

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-voorinstelling
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Voorbeeld van het genereren van een miniatuur tijdens het coderen

Hoewel in alle bovenstaande voorbeelden is besproken hoe u een coderingstaak indienen die alleen afbeeldingen produceert, u video-/audiocodering ook combineren met miniatuurgeneratie. De volgende JSON- en XML-voorinstelling vertelt **Media Encoder Standard** om een miniatuur te genereren tijdens het coderen.

### <a name="json-preset"></a><a id="json"></a>JSON-voorinstelling
Zie [dit](https://msdn.microsoft.com/library/mt269962.aspx) artikel voor informatie over het schema.

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a><a id="xml"></a>XML-voorinstelling
Zie [dit](https://msdn.microsoft.com/library/mt269962.aspx) artikel voor informatie over het schema.

```csharp
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a name="encode-video-and-generate-thumbnail-with-net"></a><a id="code_sample"></a>Video coderen en miniatuur genereren met .NET

In het volgende codevoorbeeld wordt Media Services .NET SDK gebruikt om de volgende taken uit te voeren:

* Maak een coderingstaak.
* Hier krijg je een verwijzing naar de Media Encoder Standard encoder.
* Laad de vooraf ingestelde [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) of [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) die de coderingsvoorinstelling bevat, evenals informatie die nodig is om miniaturen te genereren. U deze [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) of [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) opslaan in een bestand en de volgende code gebruiken om het bestand te laden.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Voeg één coderingstaak toe aan de taak. 
* Geef het invoerelement op dat moet worden gecodeerd.
* Maak een uitvoerelement dat het gecodeerde element bevat.
* Voeg een gebeurtenishandler toe om de voortgang van de taak te controleren.
* Verzend de taak.

Zie de [mediaservices-ontwikkeling met .NET-artikel](media-services-dotnet-how-to-use.md) voor aanwijzingen over het instellen van uw ontwikkelomgeving.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
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

        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
        Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
                    processor,
                    configuration,
                    TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                    break;
                default:
                    break;
            }
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="considerations"></a>Overwegingen
De volgende overwegingen zijn van toepassing:

* Het gebruik van expliciete tijdstempels voor Start/Stap/Bereik gaat ervan uit dat de invoerbron ten minste 1 minuut lang is.
* Jpg/Png/BmpImage-elementen hebben eigenschappen voor start-, stap- en bereiktekenreeksen:
  
  * Framenummer als het niet-negatieve gehele getallen zijn, bijvoorbeeld "Start": "120",
  * Ten opzichte van de bronduur, uitgedrukt in %-suffixed, bijvoorbeeld "Start": "15%", OF
  * Tijdstempel als uitgedrukt als HH:MM:SS... Formaat. Bijvoorbeeld "Start" : "00:01:00"
    
    U mix en match notaties als je wilt.
    
    Daarnaast ondersteunt Start ook een speciale macro:{Best}, waarin wordt geprobeerd het eerste 'interessante' frame van de inhoud TE bepalen NOTE: (Stap en bereik worden genegeerd wanneer start is ingesteld op {Best})
  * Standaardinstellingen: Start:{Best}
* Uitvoerindeling moet expliciet worden opgegeven voor elke afbeeldingsindeling: Jpg/Png/BmpFormat. Indien aanwezig, komt MES overeen met JpgVideo met JpgFormat en ga zo maar door. OutputFormat introduceert een nieuwe afbeeldingscodec specifieke macro: {Index}, die aanwezig moet zijn (eens en slechts eenmaal) voor beelduitvoerindelingen.

## <a name="next-steps"></a>Volgende stappen

U de [voortgang van](media-services-check-job-progress.md) de taak controleren terwijl de coderingstaak in behandeling is.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Overzicht van mediaservices codering](media-services-encode-asset.md)

