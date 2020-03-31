---
title: Video's bijsnijden met Media Encoder Standard - Azure | Microsoft Documenten
description: Bijsnijden is het proces waarbij een rechthoekig venster in het videoframe wordt geselecteerd en alleen de pixels in dat venster worden gecodeerd. Dit artikel laat zien hoe je video's bijsnijden met Media Encoder Standard.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 059816284e39c65bb772bd02f066d73da624722f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887761"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Video’s bijsnijden met Media Encoder Standard  

U Media Encoder Standard (MES) gebruiken om uw invoervideo bij te snijden. Bijsnijden is het proces waarbij een rechthoekig venster in het videoframe wordt geselecteerd en alleen de pixels in dat venster worden gecodeerd. Het volgende diagram helpt het proces te illustreren.

![Een video bijsnijden](./media/media-services-crop-video/media-services-crop-video01.png)

Stel dat u een video hebt met een resolutie van 1920x1080 pixels (16:9-beeldverhouding), maar links en rechts zwarte balken (pilaarvakken) heeft, zodat alleen een 4:3-venster of 1440x1080 pixels actieve video bevat. U MES gebruiken om de zwarte balken bij te snijden of te bewerken en de regio 1440x1080 te coderen.

Bijsnijden in MES is een voorbewerkingsfase, dus de bijsnijdparameters in de coderingsvoorinstelling zijn van toepassing op de oorspronkelijke invoervideo. Codering is een volgende fase en de breedte-/hoogte-instellingen zijn van toepassing op de *vooraf verwerkte* video en niet op de oorspronkelijke video. Bij het ontwerpen van de voorinstelling moet u het volgende doen: (a) selecteer de bijsnijdparameters op basis van de oorspronkelijke invoervideo en (b) selecteer uw code-instellingen op basis van de bijgesneden video. Als u uw code-instellingen niet aan de bijgesneden video aanpast, is de uitvoer niet zoals u verwacht.

In [het volgende](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) onderwerp wordt uitgelegd hoe u een coderingstaak met MES maakt en hoe u een aangepaste voorinstelling voor de coderingstaak opgeeft. 

## <a name="creating-a-custom-preset"></a>Een aangepaste voorinstelling maken
In het voorbeeld in het diagram:

1. Originele ingang is 1920x1080
2. Het moet worden bijgesneden tot een uitvoer van 1440x1080, die is gecentreerd in het invoerframe
3. Dit betekent een X-compensatie van (1920 – 1440)/2 = 240, en een Y-compensatie van nul
4. De breedte en hoogte van de rechthoek van het gewas zijn respectievelijk 1440 en 1080
5. In de codefase is het de vraag om drie lagen te produceren, resoluties 1440x1080, 960x720 en 480x360, respectievelijk

### <a name="json-preset"></a>JSON-voorinstelling
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
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
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
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
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
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


## <a name="restrictions-on-cropping"></a>Beperkingen op bijsnijden
De bijsnijdfunctie is bedoeld als handmatig. U moet uw invoervideo laden in een geschikt bewerkingsgereedschap waarmee u frames van belang selecteren, de cursor positioneren om verschuivingen voor de bijsnijdrechthoek te bepalen, om de coderingsvoorinstelling te bepalen die is afgestemd op die specifieke video, enz. Deze functie is niet bedoeld om zaken als: automatische detectie en verwijdering van zwarte brievenbus/pillarboxranden in uw invoervideo mogelijk te maken.

De volgende beperkingen zijn van toepassing op de bijsnijdfunctie. Als deze niet worden uitgevoerd, kan de codetaak mislukken of een onverwachte uitvoer produceren.

1. De coördinaten en grootte van de rechthoek bijsnijden moeten binnen de invoervideo passen
2. Zoals hierboven vermeld, moet de breedte & hoogte in de coderingsinstellingen overeenkomen met de bijgesneden video
3. Bijsnijden is van toepassing op video's die zijn vastgelegd in landschapsmodus (d.w.z. niet van toepassing op video's die zijn opgenomen met een verticaal of in portretmodus gehouden smartphone)
4. Werkt het beste met progressieve video vastgelegd met vierkante pixels

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Volgende stap
Zie leerpaden van Azure Media Services om meer te weten te komen over de geweldige functies die AMS biedt.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
