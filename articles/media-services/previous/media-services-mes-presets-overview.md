---
title: Taakvoorinstellingen voor media-encoderstandaard (MES) | Microsoft Documenten
description: Het onderwerp geeft en overzicht van de service-gedefinieerde voorbeeld presets voor Media Encoder Standard (MES).
author: Juliako
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 50c52369a5a957a4dd6279cac5079e2dea023106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463400"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Voorbeeldvoorinstellingen voor media-encoderstandaard (MES)

**Media Encoder Standard** definieert een set vooraf gedefinieerde systeemcoderingsvoorinstellingen die u gebruiken bij het maken van coderingstaken. Het wordt aanbevolen om de voorinstelling "Adaptive Streaming" te gebruiken als u een video wilt coderen voor streaming met Media Services. Wanneer u deze voorinstelling opgeeft, genereert Media Encoder Standard [automatisch een bitrate-ladder.](media-services-autogen-bitrate-ladder-with-mes.md) 

### <a name="creating-custom-presets-from-samples"></a>Aangepaste voorinstellingen maken uit voorbeelden
Media Services ondersteunt volledig het aanpassen van alle waarden in voorinstellingen om aan uw specifieke coderingsbehoeften en -vereisten te voldoen. Als u een coderingsvoorinstelling moet aanpassen, moet u beginnen met een van de onderstaande systeemvoorinstellingen die in deze sectie worden weergegeven als een sjabloon voor uw aangepaste configuratie. Zie het [schemaonderwerp Media Encoder Standard](media-services-mes-schema.md) voor uitleg over wat elk element in deze voorinstellingen betekent en de geldige waarden voor elk element.  
  
> [!NOTE]
>  Wanneer u een voorinstelling voor 4k-codeert, moet u het `S3` gereserveerde eenheidstype krijgen. Zie [Codering schalen voor](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units)meer informatie .  

#### <a name="video-rotation-default-setting-in-presets"></a>Standaardinstelling voor videorotatie in voorinstellingen:
Bij het werken met Media Encoder Standard is videorotatie standaard ingeschakeld. Als uw video is opgenomen op een mobiel apparaat in de portretmodus, worden deze voorinstellingen naar de landschapsmodus gedraaid voordat ze worden gecodeerd.
 
## <a name="available-presets"></a>Beschikbare voorinstellingen: 

 [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) produceert een set van 8 GOP-aligned MP4-bestanden, variërend van 6000 kbps tot 400 kbps en AAC 5.1-audio.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) produceert een set van 8 GOP-uitgelijnde MP4-bestanden, variërend van 6000 kbps tot 400 kbps en stereo OC-audio.  
  
 [H264 Multiple Bitrate 16x9 voor iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) produceert een set van 8 GOP-gerichte MP4-bestanden, variërend van 8500 kbps tot 200 kbps en stereo OC-audio.  
  
 [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) produceert een set van 5 GOP-aligned MP4-bestanden, variërend van 1900 kbps tot 400 kbps en AAC 5.1-audio.  
  
 [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) produceert een set van 5 GOP-aligned MP4-bestanden, variërend van 1900 kbps tot 400 kbps en stereo OC-audio.  
  
 [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) produceert een set van 12 GOP-aligned MP4-bestanden, variërend van 20000 kbps tot 1000 kbps en AAC 5.1-audio.  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) produceert een set van 12 GOP-uitgelijnde MP4-bestanden, variërend van 20000 kbps tot 1000 kbps en stereo OC-audio.  
  
 [H264 Multiple Bitrate 4x3 voor iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) produceert een set van 8 GOP-uitgelijnde MP4-bestanden, variërend van 8500 kbps tot 200 kbps en stereo OC-audio.  
  
 [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) produceert een set van 5 GOP-aligned MP4-bestanden, variërend van 1600 kbps tot 400 kbps en AAC 5.1-audio.  
  
 [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) produceert een set van 5 GOP-aligned MP4-bestanden, variërend van 1600 kbps tot 400 kbps en stereo OC-audio.  
  
 [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) produceert een set van 6 GOP-aligned MP4-bestanden, variërend van 3400 kbps tot 400 kbps en AAC 5.1-audio.  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) produceert een set van 6 GOP-uitgelijnde MP4-bestanden, variërend van 3400 kbps tot 400 kbps en stereo OC-audio.  
  
 [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) produceert één MP4-bestand met een bitrate van 6750 kbps en AAC 5.1-audio.  
  
 [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) produceert een enkel MP4-bestand met een bitrate van 6750 kbps en stereo OC-audio.  
  
 [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) produceert één MP4-bestand met een bitrate van 18000 kbps en AAC 5.1-audio.  
  
 [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) produceert één MP4-bestand met een bitrate van 18000 kbps en stereo OC-audio.  
  
 [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) produceert een enkel MP4-bestand met een bitrate van 1800 kbps en AAC 5.1-audio.  
  
 [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) produceert een enkel MP4-bestand met een bitrate van 1800 kbps en stereo OC-audio.  
  
 [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) produceert een enkel MP4-bestand met een bitrate van 2200 kbps en AAC 5.1-audio.  
  
 [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) produceert een enkel MP4-bestand met een bitrate van 2200 kbps en stereo OC-audio.  
  
 [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) produceert één MP4-bestand met een bitrate van 4500 kbps en AAC 5.1-audio.  
  
 [H264 Single Bitrate 720p voor Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) preset produceert een enkele MP4-bestand met een bitrate van 2000 kbps, en stereo OC.  
  
 [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) produceert een enkel MP4-bestand met een bitrate van 4500 kbps en stereo OC-audio.  
  
 [H264 Single Bitrate High Quality SD voor Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) produceert een enkel MP4-bestand met een bitrate van 500 kbps en stereo OC-audio..  
  
 [H264 Single Bitrate SD van lage kwaliteit voor Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) produceert één MP4-bestand met een bitrate van 56 kbps en stereo OC-audio.  
  
 Zie [On-demand coderen met Azure Media Services](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/)voor meer informatie over mediaservices- encoders.
