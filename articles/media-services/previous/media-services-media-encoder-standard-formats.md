---
title: Media Encoder Standard-indelingen en codecs - Azure
description: Dit artikel geeft een overzicht van Media Encoder Standard formaten en codecs.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: e2ccce13e4ef09426d0f3a02dcbce2f330b0ead8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251022"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard Formats and Codecs (Indelingen en codecs voor Media Encoder Standard)

> [!div class="op_single_selector" title1="Selecteer de versie van Media Services die u gebruikt:"]
> * [Versie 2](media-services-media-encoder-standard-formats.md)
> * [Versie 3](../latest/media-encoder-standard-formats.md)

Dit document bevat een lijst met de meest voorkomende import- en exportbestandsindelingen die u gebruiken met Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Invoercontainer/bestandsindelingen
| Bestandsindelingen (bestandsextensies) | Ondersteund |
| --- | --- |
| FLV (met H.264- en AAC-codecs) (.flv) |Ja |
| MXF (.mxf) |Ja |
| GXF    (.gxf) |Ja |
| MPEG2-PS, MPEG2-TS 3GP (.ts, PS, .3gp, .3gpp, mpg) |Ja |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Ja |
| AVI (niet-gecomprimeerd, 8-bits/10 bits) (.avi) |Ja |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Ja |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (dvr-ms) |Ja |
| Matroska/WebM (.mkv) |Ja |
| WAVE/WAV (.wav) |Ja |
| QuickTime (.mov) |Ja |

> [!NOTE]
> Hierboven vindt u een lijst van de meer frequent voorkomende bestandsextensies. Media Encoder Standard ondersteunt vele anderen (bijvoorbeeld: .m2ts, .mpeg2video, .qt). Als u een bestand probeert te coderen en u een foutmelding krijgt over de indeling die niet wordt ondersteund, geeft u [hier](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)uw feedback .
> 
> 

### <a name="audio-formats-in-input-containers"></a>Audio-indelingen in invoercontainers
Media Encoder Standard ondersteunt het dragen van de volgende audioformaten in invoercontainers:

* MXF-, GXF- en QuickTime-bestanden, die audiotracks met interleaved stereo of 5.1-samples hebben

of

* MXF-, GXF- en QuickTime-bestanden waar de audio wordt uitgevoerd als afzonderlijke PCM-sporen, maar de kanaaltoewijzing (in stereo of 5.1) kan worden afgeleid van de metagegevens van het bestand

## <a name="input-video-codecs"></a>Videocodecs invoeren
| Videocodecs invoeren | Ondersteund |
| --- | --- |
| AVC 8-bits/10-bits, maximaal 4:2:2, inclusief AVCIntra |8-bits 4:2:0 en 4:2:2 |
| Avid DNxHD (in MXF) |Ja |
| DVCPro/DVCProHD (in MXF) |Ja |
| Digitale video (DV) (in AVI-bestanden) |Ja |
| JPEG 2000 |Ja |
| MPEG-2 (maximaal 422-profiel en hoog niveau, inclusief varianten zoals XDCAM, XDCAM HD XDCAM IMX, CableLabs® en D10) |Maximaal 422-profiel |
| MPEG-1 |Ja |
| VC-1/WMV9 |Ja |
| Canopus HQ/HQX |Nee |
| MPEG-4 Part 2 |Ja |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ja |
| YUV420 ongecomprimeerd of mezzanine |Ja |
| Apple ProRes 422 |Ja |
| Apple ProRes 422 LT |Ja |
| Apple ProRes 422 HQ |Ja |
| Apple ProRes Proxy |Ja |
| Apple ProRes 4444 |Ja |
| Apple ProRes 4444 XQ |Ja |
| HEVC/H.265| Hoofd- en hoofdprofielen (&#42;)<br/>Ondersteuning voor het hoofdprofiel 10 is bedoeld voor 8bit 4:2:0-inhoud. |

## <a name="input-audio-codecs"></a>Audiocodecs invoeren
| Codecs audio-invoer | Ondersteund |
| --- | --- |
| AAC (AAC-LC, AAC-HE en AAC-HEv2; tot. 5.1) |Ja |
| MPEG Layer 2 |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Ja |
| Windows Media Audio |Ja |
| WAV/PCM |Ja |
| [Flac](https://en.wikipedia.org/wiki/FLAC)</a> |Ja |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Ja |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ja |
| AMR (Adaptive Multi-Rate) |Ja |
| AES (SMPTE 331M en 302M, AES3-2003) |Nee |
| Dolby® E |Nee |
| Dolby® Digital (AC3) |Nee |
| Dolby® Digital Plus (E-AC3) |Nee |

## <a name="output-formats-and-codecs"></a>Uitvoernotaties en codecs
In de volgende tabel worden de codecs en bestandsindelingen weergegeven die worden ondersteund voor export.

| Bestandsindeling | Videocodec | Audiocodec |
| --- | --- | --- |
| MP4 MP4 <br/><br/>(inclusief multibitrate MP4-containers) |H.264 (hoog-, hoofd- en basislijnprofielen) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (hoog-, hoofd- en basislijnprofielen) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[On-demand content coderen met Azure Media Services](media-services-encode-asset.md)

[Coderen met Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

