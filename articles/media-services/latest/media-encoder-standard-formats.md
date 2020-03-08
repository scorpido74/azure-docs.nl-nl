---
title: Standaard indelingen en-codecs voor encoders-Azure
description: Dit artikel bevat een lijst met de meest voorkomende indelingen voor het importeren en exporteren van bestanden die u kunt gebruiken met StandardEncoderPreset.
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
ms.date: 02/10/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: f1d4d4f4006702ebe0d057e56cf24a022e73b83e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363956"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standaard indelingen en-codecs voor encoders

Dit artikel bevat een lijst met de meest voorkomende indelingen voor het importeren en exporteren van bestanden die u kunt gebruiken met [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Zie [een trans formatie maken met een aangepaste voor instelling](customize-encoder-presets-how-to.md)voor informatie over het maken van aangepaste voor instellingen met behulp van **StandardEncoderPreset**.

## <a name="input-containerfile-formats"></a>Invoer van container/bestands indelingen

| Bestands indelingen (bestands extensies) | Ondersteund |
| --- | --- |
| FLV (met H. 264-en AAC-codecs) (. FLV) |Ja |
| MXF    (.mxf) |Ja |
| GXF (. GXF) |Ja |
| MPEG2-PS, MPEG2-TS, 3GP (. TS,. ps,. 3gp,. 3GPP,. mpg) |Ja |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Ja |
| AVI (uncompressed 8bit/10bit) (. AVI) |Ja |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Ja |
| [Digitale video-opname van micro soft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (. DVR-MS) |Ja |
| Matroska/WebM (.mkv) |Ja |
| WAVE/WAV (. wav) |Ja |
| QuickTime (. MOV) |Ja |

### <a name="audio-formats-in-input-containers"></a>Audio-indelingen in invoer containers

Standard encoder ondersteunt het uitvoeren van de volgende audio-indelingen in invoer containers:

* MXF-, GXF-en QuickTime-bestanden met audio sporen met Interleaved stereo of 5,1 voor beelden

of

* MXF-, GXF-en QuickTime-bestanden waar de audio wordt uitgevoerd als afzonderlijke PCM-tracks, maar de kanaal toewijzing (naar stereo of 5,1) kan worden afgeleid van de meta gegevens van het bestand

## <a name="input-video-codecs"></a>Video-codecs invoeren
| Video-codecs invoeren | Ondersteund |
| --- | --- |
| AVC 8-bits/10-bits, tot 4:2:2, inclusief AVCIntra |8-bits 4:2:0 en 4:2:2 |
| Avid DNxHD (in MXF) |Ja |
| DVCPro/DVCProHD (in MXF) |Ja |
| Digitale video (DV) (in AVI-bestanden) |Ja |
| JPEG 2000 |Ja |
| MPEG-2 (Maxi maal 422 profiel en hoog niveau; inclusief varianten zoals XDCAM, XDCAM HD, XDCAM IMX, CableLabs® en D10) |Maxi maal 422 profiel |
| MPEG-1 |Ja |
| VC-1/WMV9 |Ja |
| Canopus hoofd kantoor/HQX |Nee |
| MPEG-4-deel 2 |Ja |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ja |
| YUV420 niet-gecomprimeerd of mezzanine |Ja |
| Apple ProRes 422 |Ja |
| Apple ProRes 422 LT |Ja |
| Apple ProRes 422 hoofd kantoor |Ja |
| Apple ProRes-Proxy |Ja |
| Apple ProRes 4444 |Ja |
| Apple ProRes 4444 XQ |Ja |
| HEVC/H.265| Hoofd profiel|

## <a name="input-audio-codecs"></a>Audio-codecs invoeren
| Audio-codecs invoeren | Ondersteund |
| --- | --- |
| AAC (AAC-LC, AAC-HE en AAC-HEv2; tot 5,1) |Ja |
| MPEG Layer 2 |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Ja |
| Windows Media Audio |Ja |
| WAV/PCM |Ja |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ja |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Ja |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ja |
| AMR (adaptief multi-rate) |Ja |
| AES (SMPTE 331M en 302M, AES3-2003) |Nee |
| Dolby® E |Nee |
| Dolby® Digital (AC3) |Nee |
| Dolby® Digital Plus (E-AC3) |Nee |

## <a name="output-formats-and-codecs"></a>Uitvoer indelingen en codecs
De volgende tabel geeft een lijst van de codecs en bestands indelingen die worden ondersteund voor het exporteren.

| Bestandsindeling | Videocodec | Audiocodec |
| --- | --- | --- |
| MP4 <br/><br/>(inclusief multi-bitrate MP4-containers) |H. 264 (High-, Main-en baseline-profielen) |AAC-LC, hij-AAC v1, HE-AAC v2 |
| MPEG2-TS |H. 264 (High-, Main-en baseline-profielen) |AAC-LC, hij-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Volgende stappen

[Een trans formatie maken met een aangepaste voor instelling](customize-encoder-presets-how-to.md)
