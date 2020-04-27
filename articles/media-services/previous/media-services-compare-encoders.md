---
title: Vergelijking van media encoders op aanvraag van Azure Microsoft Docs
description: In dit onderwerp worden de coderings mogelijkheden van **Media Encoder Standard** en **Media Encoder Premium workflow**vergeleken.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 4767f7bb5ba02c838c0e21721e55a6564a14acd1
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "69016658"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Vergelijking van media encoders op aanvraag van Azure  

In dit onderwerp worden de coderings mogelijkheden van **Media Encoder Standard** en **Media Encoder Premium workflow**vergeleken.

## <a name="video-and-audio-processing-capabilities"></a>Mogelijkheden voor video-en audio verwerking

De volgende tabel vergelijkt de functionaliteit van Media Encoder Standard (MES) en Media Encoder Premium Workflow (MEPW). 

|Mogelijkheid|Media Encoder Standard|Media Encoder Premium Workflow|
|---|---|---|
|Voorwaardelijke logica Toep assen tijdens het coderen<br/>(als de invoer bijvoorbeeld HD is, codeer dan 5,1 audio)|Nee|Ja|
|Ondertiteling|Nee|[Ja](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Loudness correctie voor Dolby® Professional](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> met dialoog Intelligence™|Nee|Ja|
|Interliniëring, inverse telecine|Basic|Broadcast kwaliteit|
|Zwarte randen detecteren en verwijderen <br/>(pillarboxes, letterboxes)|Nee|Ja|
|Miniatuur genereren|[Ja](media-services-dotnet-generate-thumbnail-with-mes.md)|[Ja](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Video's knippen/bijsnijden en samen voegen|[Ja](media-services-advanced-encoding-with-mes.md#trim_video)|Ja|
|Overlays van audio of video|[Ja](media-services-advanced-encoding-with-mes.md#overlay)|[Ja](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Overlays van afbeeldingen|Uit afbeeldings bronnen|Van afbeeldings-en tekst bronnen|
|Meerdere audio taal sporen|Beperkt|[Ja](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>Facturerings meter die wordt gebruikt door elk coderings programma
| Naam van media processor | Toepasselijke prijzen | Opmerkingen |
| --- | --- | --- |
| **Media Encoder Standard** |RING |De kosten voor het coderen van taken worden berekend op basis van de totale duur, in minuten, van alle media bestanden die worden geproduceerd als uitvoer, tegen de snelheid die u [hier][1]opgeeft, onder de kolom encoder. |
| **Media Encoder Premium Workflow** |PREMIUM ENCODER |De kosten voor het coderen van taken worden berekend op basis van de totale duur, in minuten, van alle media bestanden die worden geproduceerd als uitvoer, tegen de snelheid die u [hier][1]opgeeft, onder de kolom Premium encoder. |

## <a name="input-containerfile-formats"></a>Invoer van container/bestands indelingen
| Invoercontainer/bestandsindelingen | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| F4V van Adobe® Flash® |Ja |Ja |
| MXF/SMPTE 377M |Ja |Ja |
| GXF |Ja |Ja |
| MPEG-2-transport stromen |Ja |Ja |
| MPEG-2-programma-streams |Ja |Ja |
| MPEG-4/MP4 |Ja |Ja |
| Windows Media/ASF |Ja |Ja |
| AVI (niet-gecomprimeerd 8bit/10bit) |Ja |Ja |
| 3GPP/3GPP2 |Ja |Nee |
| Smooth Streaming bestands indeling (PIFF 1,3) |Ja |Nee |
| [Digitale video-opname van micro soft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Ja |Nee |
| Matroska/WebM |Ja |Nee |
| QuickTime (.mov) |Ja |Nee |

## <a name="input-video-codecs"></a>Video-codecs invoeren
| Codecs invoervideo | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC 8-bits/10-bits, maximaal 4:2:2, inclusief AVCIntra |8-bits 4:2:0 en 4:2:2 |Ja |
| Avid DNxHD (in MXF) |Ja |Ja |
| DVCPro/DVCProHD (in MXF) |Ja |Ja |
| JPEG2000 |Ja |Ja |
| MPEG-2 (Maxi maal 422 profiel en hoog niveau; inclusief varianten zoals XDCAM, XDCAM HD, XDCAM IMX, CableLabs® en D10) |Maximaal 422-profiel |Ja |
| MPEG-1 |Ja |Ja |
| Windows Media Video/VC-1 |Ja |Ja |
| Canopus HQ/HQX |Nee |Nee |
| MPEG-4 Part 2 |Ja |Nee |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ja |Nee |
| Apple ProRes 422 |Ja |Nee |
| Apple ProRes 422 LT |Ja |Nee |
| Apple ProRes 422 HQ |Ja |Nee |
| Apple ProRes Proxy |Ja |Nee |
| Apple ProRes 4444 |Ja |Nee |
| Apple ProRes 4444 XQ |Ja |Nee |
| HEVC/H. 265|Hoofd profiel|Hoofd-en hoofd-10-profiel|

## <a name="input-audio-codecs"></a>Audio-codecs invoeren
| Codecs audio-invoer | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M en 302M, AES3-2003) |Nee |Ja |
| Dolby® E |Nee |Ja |
| Dolby® Digital (AC3) |Nee |Ja |
| Dolby® Digital Plus (E-AC3) |Nee |Ja |
| AAC (AAC-LC, AAC-HE en AAC-HEv2; tot. 5.1) |Ja |Ja |
| MPEG Layer 2 |Ja |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Ja |Ja |
| Windows Media Audio |Ja |Ja |
| WAV/PCM |Ja |Ja |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ja |Nee |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Ja |Nee |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ja |Nee |

## <a name="output-containerfile-formats"></a>Uitvoer container/bestands indelingen
| Uitvoer container/bestands indelingen | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| F4V van Adobe® Flash® |Nee |Ja |
| MXF (OP1a, XDCAM en AS02) |Nee |Ja |
| DPP (inclusief AS11) |Nee |Ja |
| GXF |Nee |Ja |
| MPEG-4/MP4 |Ja |Ja |
| MPEG-TS |Ja |Ja |
| Windows Media/ASF |Nee |Ja |
| AVI (niet-gecomprimeerd 8bit/10bit) |Nee |Ja |
| Smooth Streaming bestands indeling (PIFF 1,3) |Nee |Ja |

## <a name="output-video-codecs"></a>Video-codecs voor uitvoer
| Video-codecs voor uitvoer | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC (H. 264; 8-bits; tot hoog profiel, niveau 5,2; 4 KB Ultra HD; AVC-Intra) |Alleen 8-bits 4:2:0 |Ja |
| HEVC (H. 265; 8-bits en 10-bits;)  |Nee |Ja |
| Avid DNxHD (in MXF) |Nee |Ja |
| MPEG-2 (Maxi maal 422 profiel en hoog niveau; inclusief varianten zoals XDCAM, XDCAM HD, XDCAM IMX, CableLabs® en D10) |Nee |Ja |
| MPEG-1 |Nee |Ja |
| Windows Media Video/VC-1 |Nee |Ja |
| JPEG-miniatuur maken |Ja |Ja |
| Miniatuur maken voor PNG |Ja |Ja |
| Miniatuur maken van BMP |Ja |Nee |

## <a name="output-audio-codecs"></a>Audio-codecs voor uitvoer
| Audio-codecs voor uitvoer | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M en 302M, AES3-2003) |Nee |Ja |
| Dolby® Digital (AC3) |Nee |Ja |
| Dolby® Digital Plus (E-AC3) tot 7,1 |Nee |Ja |
| AAC (AAC-LC, AAC-HE en AAC-HEv2; tot. 5.1) |Ja |Ja |
| MPEG Layer 2 |Nee |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Nee |Ja |
| Windows Media Audio |Nee |Ja |

>[!NOTE]
>Als u codeert naar Dolby® Digital (AC3), kan de uitvoer alleen naar een ISO MP4-bestand worden geschreven.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Verwante artikelen:
* [Geavanceerde coderings taken uitvoeren door Media Encoder Standard voor instellingen aan te passen](media-services-custom-mes-presets-with-dotnet.md)
* [Quota en beperkingen](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
