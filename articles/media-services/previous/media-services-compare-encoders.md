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
ms.openlocfilehash: f42361df438a434548b3bc9394c007ef8d4c6eb0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038967"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Vergelijking van media encoders op aanvraag van Azure  

In dit onderwerp worden de coderings mogelijkheden van **Media Encoder Standard** en **Media Encoder Premium workflow**vergeleken.

## <a name="video-and-audio-processing-capabilities"></a>Mogelijkheden voor video-en audio verwerking

De volgende tabel vergelijkt de functionaliteit van Media Encoder Standard (MES) en Media Encoder Premium Workflow (MEPW). 

|Mogelijkheid|Media Encoder Standard|Media Encoder Premium Workflow|
|---|---|---|
|Voorwaardelijke logica Toep assen tijdens het coderen<br/>(als de invoer bijvoorbeeld HD is, codeer dan 5,1 audio)|Nee|Yes|
|Ondertiteling|No|[Ja](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[&reg;LOUDNESS correctie Dolby Professional](https://professional.dolby.com/product/broadcast/vm600/)<br/> met dialoog informatie&trade;|Nee|Yes|
|Interliniëring, inverse telecine|Basic|Broadcast kwaliteit|
|Zwarte randen detecteren en verwijderen <br/>(pillarboxes, letterboxes)|Nee|Yes|
|Miniatuur genereren|[Ja](media-services-dotnet-generate-thumbnail-with-mes.md)|[Ja](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Video's knippen/bijsnijden en samen voegen|[Ja](media-services-advanced-encoding-with-mes.md#trim_video)|Yes|
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
| Adobe &reg; Flash &reg; F4V |Yes |Ja |
| MXF/SMPTE 377M |Yes |Ja |
| GXF |Yes |Ja |
| MPEG-2-transport stromen |Yes |Ja |
| MPEG-2-programma-streams |Yes |Ja |
| MPEG-4/MP4 |Yes |Ja |
| Windows Media/ASF |Yes |Ja |
| AVI (niet-gecomprimeerd 8bit/10bit) |Yes |Ja |
| 3GPP/3GPP2 |Yes |Nee |
| Smooth Streaming bestands indeling (PIFF 1,3) |Yes |Nee |
| [Digitale video-opname van micro soft (DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) |Yes |Nee |
| Matroska/WebM |Yes |Nee |
| QuickTime (.mov) |Yes |Nee |

## <a name="input-video-codecs"></a>Video-codecs invoeren
| Codecs invoervideo | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC 8-bits/10-bits, maximaal 4:2:2, inclusief AVCIntra |8-bits 4:2:0 en 4:2:2 |Yes |
| Avid DNxHD (in MXF) |Ja |Ja |
| DVCPro/DVCProHD (in MXF) |Ja |Ja |
| JPEG2000 |Ja |Ja |
| MPEG-2 (Maxi maal 422 profiel en hoog niveau; inclusief varianten zoals XDCAM, XDCAM HD, XDCAM IMX, CableLabs &reg; en D10) |Maximaal 422-profiel |Yes |
| MPEG-1 |Ja |Ja |
| Windows Media Video/VC-1 |Ja |Ja |
| Canopus HQ/HQX |Nee |Nee |
| MPEG-4 Part 2 |Yes |No |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |No |
| Apple ProRes 422 |Yes |No |
| Apple ProRes 422 LT |Yes |No |
| Apple ProRes 422 HQ |Yes |No |
| Apple ProRes Proxy |Yes |No |
| Apple ProRes 4444 |Yes |No |
| Apple ProRes 4444 XQ |Yes |No |
| HEVC/H. 265|Hoofd profiel|Hoofd-en hoofd-10-profiel|

## <a name="input-audio-codecs"></a>Audio-codecs invoeren
| Codecs audio-invoer | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M en 302M, AES3-2003) |No |Yes |
| Dolby &reg; E |No |Yes |
| Dolby &reg; Digital (AC3) |No |Yes |
| Dolby &reg; Digital Plus (E-AC3) |No |Yes |
| AAC (AAC-LC, AAC-HE en AAC-HEv2; tot. 5.1) |Ja |Ja |
| MPEG Layer 2 |Ja |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Ja |Ja |
| Windows Media Audio |Ja |Ja |
| WAV/PCM |Ja |Ja |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |No |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Yes |No |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |No |

## <a name="output-containerfile-formats"></a>Uitvoer container/bestands indelingen
| Uitvoer container/bestands indelingen | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| Adobe &reg; Flash &reg; F4V |No |Yes |
| MXF (OP1a, XDCAM en AS02) |No |Yes |
| DPP (inclusief AS11) |Nee |Yes |
| GXF |Nee |Yes |
| MPEG-4/MP4 |Ja |Ja |
| MPEG-TS |Ja |Ja |
| Windows Media/ASF |Nee |Yes |
| AVI (niet-gecomprimeerd 8bit/10bit) |Nee |Yes |
| Smooth Streaming bestands indeling (PIFF 1,3) |Nee |Yes |

## <a name="output-video-codecs"></a>Video-codecs voor uitvoer
| Video-codecs voor uitvoer | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC (H. 264; 8-bits; tot hoog profiel, niveau 5,2; 4 KB Ultra HD; AVC-Intra) |Alleen 8-bits 4:2:0 |Yes |
| HEVC (H. 265; 8-bits en 10-bits;)  |Nee |Yes |
| Avid DNxHD (in MXF) |Nee |Yes |
| MPEG-2 (Maxi maal 422 profiel en hoog niveau; inclusief varianten zoals XDCAM, XDCAM HD, XDCAM IMX, CableLabs &reg; en D10) |Nee |Yes |
| MPEG-1 |Nee |Yes |
| Windows Media Video/VC-1 |Nee |Yes |
| JPEG-miniatuur maken |Ja |Ja |
| Miniatuur maken voor PNG |Ja |Ja |
| Miniatuur maken van BMP |Yes |Nee |

## <a name="output-audio-codecs"></a>Audio-codecs voor uitvoer
| Audio-codecs voor uitvoer | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M en 302M, AES3-2003) |Nee |Yes |
| Dolby &reg; Digital (AC3) |Nee |Yes |
| Dolby &reg; Digital Plus (E-AC3) tot 7,1 |Nee |Yes |
| AAC (AAC-LC, AAC-HE en AAC-HEv2; tot. 5.1) |Ja |Ja |
| MPEG Layer 2 |Nee |Yes |
| MP3 (MPEG-1 Audio Layer 3) |Nee |Yes |
| Windows Media Audio |Nee |Yes |

>[!NOTE]
>Als u codeert naar Dolby &reg; Digital (AC3), kan de uitvoer alleen naar een ISO MP4-bestand worden geschreven.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Verwante artikelen:
* [Geavanceerde coderings taken uitvoeren door Media Encoder Standard voor instellingen aan te passen](media-services-custom-mes-presets-with-dotnet.md)
* [Quota en beperkingen](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
