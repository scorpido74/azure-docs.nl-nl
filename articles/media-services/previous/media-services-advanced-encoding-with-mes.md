---
title: Geavanceerde codering uitvoeren door MES-voorinstellingen aan te maken | Microsoft Documenten
description: In dit onderwerp ziet u hoe u geavanceerde codering uitvoeren door voorinstellingen voor mediaencoderstandaardtes aan te werken.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 5f7611fd9df207df51fa0e51218d8a234583b1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529780"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Geavanceerde codering uitvoeren door MES-voorinstellingen aan te vullen 

## <a name="overview"></a>Overzicht

In dit onderwerp ziet u hoe u voorinstellingen van Media Encoder Standard aanpassen. In het onderwerp [Codering met Media Encoder Standard met behulp van aangepaste voorinstellingen](media-services-custom-mes-presets-with-dotnet.md) ziet u hoe u .NET gebruiken om een coderingstaak en een taak te maken die deze taak uitvoert. Zodra u een voorinstelling hebt aangepast, levert u de aangepaste voorinstellingen aan de coderingstaak. 

Als u een XML-voorinstelling gebruikt, moet u de volgorde van elementen behouden, zoals weergegeven in XML-voorbeelden hieronder (bijvoorbeeld Hoofdframeinterval moet voorafgaan aan ScèneChangeDetection).

> [!NOTE] 
> Veel van de geavanceerde Media Services v2-functies van de Media Encoder Standard zijn momenteel niet beschikbaar in v3. Zie [functiehiaten voor](https://docs.microsoft.com/azure/media-services/latest/media-services-v2-vs-v3#feature-gaps-with-respect-to-v2-apis)meer informatie .

## <a name="support-for-relative-sizes"></a>Ondersteuning voor relatieve grootte

Bij het genereren van miniaturen hoeft u niet altijd uitvoerbreedte en -hoogte in pixels op te geven. U ze opgeven in percentages, in het bereik [1%,..., 100%].

### <a name="json-preset"></a>JSON-voorinstelling
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>XML-voorinstelling
    <Width>100%</Width>
    <Height>100%</Height>

## <a name="generate-thumbnails"></a><a id="thumbnails"></a>Miniaturen genereren

In deze sectie ziet u hoe u een voorinstelling aanpast die miniaturen genereert. De vooraf ingestelde hieronder bevat informatie over hoe u uw bestand wilt coderen, evenals informatie die nodig is om miniaturen te genereren. U een van de MES-voorinstellingen die [deze](media-services-mes-presets-overview.md) sectie hebben gedocumenteerd, gebruiken en code toevoegen die miniaturen genereert.  

> [!NOTE]
> De instelling **ScèneChangeDetection** in de volgende voorinstelling kan alleen worden ingesteld op true als u codeert voor één bitratevideo. Als u codeert voor een video met meerdere bitrates en **SceneChangeDetection** op true instelt, geeft de encoder een fout.  
>
>

Zie [dit](media-services-mes-schema.md) onderwerp voor informatie over het schema.

Controleer de sectie [Overwegingen.](#considerations)

### <a name="json-preset"></a><a id="json"></a>JSON-voorinstelling
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
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
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
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a name="xml-preset"></a><a id="xml"></a>XML-voorinstelling
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
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

### <a name="considerations"></a>Overwegingen

De volgende overwegingen zijn van toepassing:

* Het gebruik van expliciete tijdstempels voor Start/Stap/Bereik gaat ervan uit dat de invoerbron ten minste 1 minuut lang is.
* Jpg/Png/BmpImage-elementen hebben eigenschappen voor start-, stap- en bereiktekenreeksen:

  * Framenummer als het niet-negatieve gehele getallen zijn, bijvoorbeeld "Start": "120",
  * Ten opzichte van de bronduur, uitgedrukt in %-suffixed, bijvoorbeeld "Start": "15%", OF
  * Tijdstempel als uitgedrukt als HH:MM:SS... opmaak, bijvoorbeeld "Start": "00:01:00"

    U mix en match notaties als je wilt.

    Daarnaast ondersteunt Start ook een speciale macro:{Best}, waarin wordt geprobeerd het eerste 'interessante' frame van de inhoud TE bepalen NOTE: (Stap en bereik worden genegeerd wanneer start is ingesteld op {Best})
  * Standaardinstellingen: Start:{Best}
* Uitvoerindeling moet expliciet worden opgegeven voor elke afbeeldingsindeling: Jpg/Png/BmpFormat. Indien aanwezig, komt MES overeen met JpgVideo met JpgFormat en ga zo maar door. OutputFormat introduceert een nieuwe afbeeldingscodec specifieke macro: {Index}, die aanwezig moet zijn (eens en slechts eenmaal) voor beelduitvoerindelingen.

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>Een video bijsnijden (knippen)
In deze sectie wordt gesproken over het wijzigen van de encodervoorinstellingen om de invoervideo te knippen of bij te snijden wanneer de invoer een zogenaamd mezzaninebestand of on-demandbestand is. De encoder kan ook worden gebruikt voor het knippen of trimmen van een asset, die wordt vastgelegd of gearchiveerd via een live stream - de details hiervoor zijn beschikbaar in [deze blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Als u uw video's wilt bijsnijden, u een van de MES-voorinstellingen die [deze](media-services-mes-presets-overview.md) sectie hebben gedocumenteerd, het element **Bronnen** wijzigen (zoals hieronder wordt weergegeven). De waarde van StartTime moet overeenkomen met de absolute tijdstempels van de invoervideo. Als het eerste frame van de invoervideo bijvoorbeeld een tijdstempel van 12:00:10.000 heeft, moet StartTime ten minste 12:00:10.000 en hoger zijn. In het onderstaande voorbeeld gaan we ervan uit dat de invoervideo een starttijdstempel van nul heeft. **Bronnen** moeten aan het begin van de voorinstelling worden geplaatst.

### <a name="json-preset"></a><a id="json"></a>JSON-voorinstelling
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="xml-preset"></a>XML-voorinstelling
Als u uw video's wilt bijsnijden, u een van de [hier](media-services-mes-presets-overview.md) gedocumenteerde MES-voorinstellingen nemen en het element **Bronnen** wijzigen (zoals hieronder wordt weergegeven).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
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
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

## <a name="create-an-overlay"></a><a id="overlay"></a>Een overlay maken

Met de Media Encoder Standard u een afbeelding op een bestaande video plaatsen. Momenteel worden de volgende indelingen ondersteund: png, jpg, gif en bmp. De vooraf ingestelde hieronder gedefinieerd is een basisvoorbeeld van een video overlay.

Naast het definiëren van een vooraf ingesteld bestand, moet u Media Services ook laten weten welk bestand in de overlayafbeelding de overlayafbeelding is en welk bestand de bronvideo is waarop u de afbeelding wilt bedekken. Het videobestand moet het **primaire** bestand zijn.

Als u .NET gebruikt, voegt u de volgende twee functies toe aan het .NET-voorbeeld dat in [dit](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) onderwerp is gedefinieerd. De functie **UploadMediaFilesFromFolder** uploadt bestanden uit een map (bijvoorbeeld BigBuckBunny.mp4 en Image001.png) en stelt het mp4-bestand in als het primaire bestand in de asset. De functie **EncodeWithOverlay** gebruikt het aangepaste voorinstellingsbestand dat is doorgegeven (bijvoorbeeld de voorinstelling die volgt) om de coderingstaak te maken.


    static public IAsset UploadMediaFilesFromFolder(string folderPath)
    {
        IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
    
        foreach (var af in asset.AssetFiles)
        {
            // The following code assumes 
            // you have an input folder with one MP4 and one overlay image file.
            if (af.Name.Contains(".mp4"))
                af.IsPrimary = true;
            else
                af.IsPrimary = false;
    
            af.Update();
        }
    
        return asset;
    }

    static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("Media Encoder Standard Job");
        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(customPresetFileName);

        // Create a task
        ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input assets to be encoded.
        // This asset contains a source file and an overlay file.
        task.InputAssets.Add(assetSource);

        // Add an output asset to contain the results of the job. 
        task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }


> [!NOTE]
> Huidige beperkingen:
>
> De dekkingsinstelling voor overlay wordt niet ondersteund.
>
> Uw bronvideobestand en het overlay-afbeeldingsbestand moeten in hetzelfde element staan en het videobestand moet worden ingesteld als het primaire bestand in dit element.
>
>

### <a name="json-preset"></a>JSON-voorinstelling
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a name="xml-preset"></a>XML-voorinstelling
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>Een stille audiotrack invoegen wanneer invoer geen audio heeft
Als u standaard een invoer verzendt naar de encoder die alleen video en geen audio bevat, bevat het uitvoeritem bestanden die alleen videogegevens bevatten. Sommige spelers kunnen dergelijke uitvoerstromen mogelijk niet verwerken. U deze instelling gebruiken om de encoder te dwingen een stille audiotrack toe te voegen aan de uitvoer in dat scenario.

Als u de encoder wilt dwingen een asset te produceren die een stille audiotrack bevat wanneer invoer geen audio heeft, geeft u de waarde 'InsertSilenceIfNoAudio' op.

U een van de MES-voorinstellingen die in [deze](media-services-mes-presets-overview.md) sectie zijn gedocumenteerd, bekijken en de volgende wijziging aanbrengen:

### <a name="json-preset"></a>JSON-voorinstelling
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>XML-voorinstelling
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>Automatische ontvlechting uitschakelen
Klanten hoeven niets te doen als ze willen dat de interlace-inhoud automatisch wordt ontvlecht. Wanneer de automatische ontvlechting is ingeschakeld (standaard) doet de MES de automatische detectie van interlaced frames en alleen de-interlaces frames gemarkeerd als interlaced.

U de automatische de-interlacing uitschakelen. Deze optie wordt niet aanbevolen.

### <a name="json-preset"></a>JSON-voorinstelling
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>XML-voorinstelling
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a name="audio-only-presets"></a><a id="audio_only"></a>Voorinstellingen voor audio alleen
In dit gedeelte worden twee MES-voorinstellingen voor audio getoond: AAC Audio en AAC Good Quality Audio.

### <a name="aac-audio"></a>Oc-audio
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="aac-good-quality-audio"></a>AAC Audio van goede kwaliteit
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>Twee of meer videobestanden inconcereren

In het volgende voorbeeld wordt uitgelegd hoe u een voorinstelling genereren om twee of meer videobestanden te laten aansluiten. Het meest voorkomende scenario is wanneer u een koptekst of een trailer aan de hoofdvideo wilt toevoegen. Het beoogde gebruik is wanneer de videobestanden die samen worden bewerkt, eigenschappen delen (videoresolutie, framesnelheid, aantal audiosporen, enz.). Zorg ervoor dat u geen video's van verschillende framesnelheden of met verschillende audiotracks mengt.

>[!NOTE]
>Het huidige ontwerp van de concatenatiefunctie verwacht dat de invoervideoclips consistent zijn in termen van resolutie, framesnelheid enz. 

### <a name="requirements-and-considerations"></a>Eisen en overwegingen

* Invoervideo's mogen slechts één audiotrack hebben.
* Invoervideo's moeten allemaal dezelfde framesnelheid hebben.
* Je moet je video's uploaden naar afzonderlijke elementen en de video's instellen als het primaire bestand in elk item.
* Je moet de duur van je video's weten.
* De vooraf ingestelde voorbeelden hieronder gaat ervan uit dat alle invoervideo's beginnen met een tijdstempel van nul. U moet de StartTime-waarden wijzigen als de video's een andere starttijdstempel hebben, zoals meestal het geval is bij live-archieven.
* De JSON-voorinstelling verwijst expliciet naar de AssetID-waarden van de invoerelementen.
* De voorbeeldcode gaat ervan uit dat de JSON-voorinstelling is opgeslagen in een lokaal bestand, zoals "C:\supportFiles\preset.json". Het gaat er ook van uit dat twee elementen zijn gemaakt door het uploaden van twee videobestanden, en dat u de resulterende AssetID-waarden kent.
* Het codefragment en de JSON-voorinstelling toont een voorbeeld van het gelijktijdig maken van twee videobestanden. Je het uitbreiden naar meer dan twee video's door:

  1. Oproeptaak. InputAssets.Add() herhaaldelijk om meer video's toe te voegen, in volgorde.
  2. Het aanbrengen van overeenkomstige bewerkingen aan het element "Bronnen" in de JSON, door meer vermeldingen toe te voegen, in dezelfde volgorde.

### <a name="net-code"></a>.NET-code

    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job.
    // This output is specified as AssetCreationOptions.None, which
    // means the output asset is not encrypted.
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

### <a name="json-preset"></a>JSON-voorinstelling

Werk uw aangepaste voorinstelling bij met id's van de elementen die u wilt samenbrengen en met het juiste tijdssegment voor elke video.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
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
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Video's bijsnijden met Media Encoder Standard
Bekijk het [crop-video's met het onderwerp Media Encoder Standard.](media-services-crop-video.md)

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>Een videotrack invoegen wanneer invoer geen video heeft

Als u standaard een invoer verzendt naar de encoder die alleen audio en geen video bevat, bevat het uitvoeritem bestanden die alleen audiogegevens bevatten. Sommige spelers, waaronder Azure Media Player (zie [dit)](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)kunnen dergelijke streams mogelijk niet verwerken. U deze instelling gebruiken om de encoder te dwingen een monochrome videotrack toe te voegen aan de uitvoer in dat scenario.

> [!NOTE]
> Als u de encoder dwingt een uitvoervideotrack in te voegen, wordt de grootte van het uitvoeractief vergroot en daardoor de kosten voor de coderingstaak. U moet tests uitvoeren om te controleren of deze resulterende stijging slechts een bescheiden impact heeft op uw maandelijkse kosten.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Video invoegen met alleen de laagste bitrate

Stel dat u een voorinstelling voor meervoudige bitratecodering gebruikt, zoals ["H264 Multiple Bitrate 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) om uw volledige invoercatalogus te coderen voor streaming, die een mix van videobestanden en bestanden met alleen audio bevat. In dit scenario, wanneer de invoer geen video heeft, u de encoder dwingen om een monochrome videotrack in te voegen op slechts de laagste bitrate, in tegenstelling tot het invoegen van video bij elke uitvoerbitrate. Om dit te bereiken, moet u de **vlag InsertBlackIfNoVideoBottomLayerOnly** gebruiken.

U een van de MES-voorinstellingen die in [deze](media-services-mes-presets-overview.md) sectie zijn gedocumenteerd, bekijken en de volgende wijziging aanbrengen:

#### <a name="json-preset"></a>JSON-voorinstelling
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-voorinstelling

Gebruik Condition="InsertBlackIfNoVideoBottomLayerOnly" bij gebruik als kenmerk van het **element H264Video** en Condition="InsertSilenceIfNoAudio" als kenmerk van **AACAudio**.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Video invoegen bij alle uitvoerbitrates
Stel dat u een voorinstelling voor meervoudige bitratecodering gebruikt, zoals ["H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) om uw volledige invoercatalogus te coderen voor streaming, die een mix van videobestanden en bestanden met alleen audio bevat. In dit scenario u, wanneer de invoer geen video heeft, de encoder dwingen om een monochrome videotrack in te voegen bij alle uitvoerbitrates. Dit zorgt ervoor dat uw uitvoerassets allemaal homogeen zijn met betrekking tot het aantal videotracks en audiotracks. Om dit te bereiken, moet u de vlag 'InsertBlackIfNoVideo' opgeven.

U een van de MES-voorinstellingen die in [deze](media-services-mes-presets-overview.md) sectie zijn gedocumenteerd, bekijken en de volgende wijziging aanbrengen:

#### <a name="json-preset"></a>JSON-voorinstelling
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-voorinstelling

Gebruik Condition="InsertBlackIfNoVideo" als kenmerk van het **element H264Video** en Condition="InsertSilenceIfNoAudio" als kenmerk van **AACAudio**.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a name="rotate-a-video"></a><a id="rotate_video"></a>Een video roteren
De [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) ondersteunt rotatie door hoeken van 0/90/180/270. Het standaardgedrag is 'Automatisch', waarbij het de rotatiemetagegevens in het binnenkomende videobestand probeert te detecteren en dit probeert te compenseren. Voeg het volgende **element Bronnen** toe aan een van de voorinstellingen die in [deze](media-services-mes-presets-overview.md) sectie zijn gedefinieerd:

### <a name="json-preset"></a>JSON-voorinstelling
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...
### <a name="xml-preset"></a>XML-voorinstelling
    <Sources>
           <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Zie [ook dit](media-services-mes-schema.md#PreserveResolutionAfterRotation) onderwerp voor meer informatie over hoe de encoder de instellingen Breedte en Hoogte interpreteert in de voorinstelling, wanneer rotatiecompensatie wordt geactiveerd.

U de waarde 0 gebruiken om aan de encoder aan te geven rotatiemetagegevens te negeren, indien aanwezig, in de invoervideo.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Overzicht van mediaservices codering](media-services-encode-asset.md)
