---
title: Standaard encoder-indelingen en codecs - Azure
description: Dit artikel bevat een lijst met de meest voorkomende import- en exportbestandsindelingen die u gebruiken met StandardEncoderPreset.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251360"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standaard encoderformaten en codecs

Dit artikel bevat een lijst met de meest voorkomende import- en exportbestandsindelingen die u gebruiken met [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Zie [Een transformatie maken met een aangepaste voorinstelling voor](customize-encoder-presets-how-to.md)informatie over het maken van aangepaste voorinstellingen met behulp van **StandardEncoderPreset.**

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

### <a name="audio-formats-in-input-containers"></a>Audio-indelingen in invoercontainers

Standard Encoder ondersteunt het dragen van de volgende audioformaten in invoercontainers:

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
| HEVC/H.265| Hoofdprofiel|

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

## <a name="next-steps"></a>Volgende stappen

[Een transformatie maken met een aangepaste voorinstelling](customize-encoder-presets-how-to.md)
