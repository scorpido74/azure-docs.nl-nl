---
title: Media Encoder Premium Workflow indelingen en codecs | Microsoft Docs
description: Dit onderwerp bevat een overzicht van Media Encoder Premium Workflow indelingen en codecs
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
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 87cd7c63939331190530a46071a6b4c40480562f
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84705097"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Media Encoder Premium Workflow indelingen en codecs

> [!NOTE]
> Media Encoder Premium Workflow media processor die in dit onderwerp wordt besproken, is niet beschikbaar in China. 

Dit document bevat een lijst met invoer-en uitvoer bestands indelingen en-codecs die worden ondersteund door de open bare preview-versie van het coderings programma **Media Encoder Premium workflow** .

[Invoer indelingen en codecs Media Encoder Premium Workflow](#input_formats)

Uitvoer indelingen en codecs Media Encoder Premium Workflow

**Media Encoder Premium workflow** ondersteunt closed captioning die in [deze](#closed_captioning) sectie wordt beschreven. 

## <a name="media-encoder-premium-workflow-input-formats-and-codecs"></a><a id="input_formats"></a>Invoer indelingen en codecs Media Encoder Premium Workflow

In de volgende sectie worden de codecs en bestands indelingen vermeld die door deze media processor als invoer worden ondersteund.

### <a name="input-containerfile-formats"></a>Invoercontainer/bestandsindelingen

* F4V van Adobe® Flash®
* MXF/SMPTE 377M
* GXF
* MPEG-2-transport stromen
* MPEG-2-programma-streams
* MPEG-4/MP4
* Windows Media/ASF
* AVI (niet-gecomprimeerd 8bit/10bit)

### <a name="input-video-codecs"></a>Codecs invoervideo

* AVC 8-bits/10-bits, maximaal 4:2:2, inclusief AVCIntra
* Avid DNxHD (in MXF)
* DVCPro/DVCProHD (in MXF)
* HEVC/H. 265, hoofd-en hoofd-10-profiel
* JPEG2000
* MPEG-2 (Maxi maal 422 profiel en hoog niveau; inclusief varianten zoals XDCAM, XDCAM HD, XDCAM IMX, CableLabs® en D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Codecs audio-invoer

* AES (SMPTE 331M en 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE en AAC-HEv2; tot. 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio
* WAV/PCM

## <a name="media-encoder-premium-workflow-output-formats-and-codecs"></a><a id="output_format"></a>Uitvoer indelingen en codecs Media Encoder Premium Workflow

In de volgende sectie worden de codecs en bestands indelingen vermeld die worden ondersteund als uitvoer van deze media processor.

### <a name="output-containerfile-formats"></a>Uitvoer container/bestands indelingen

* F4V van Adobe® Flash®
* MXF (OP1a, XDCAM en AS02)
* DPP (inclusief AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (niet-gecomprimeerd 8bit/10bit)
* Smooth Streaming bestands indeling (PIFF 1,3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Video-codecs voor uitvoer

* AVC (H. 264; 8-bits; tot hoog profiel, niveau 5,2; 4 KB Ultra HD; AVC-Intra)
* Avid DNxHD (in MXF)
* DVCPro/DVCProHD (in MXF)
* MPEG-2 (Maxi maal 422 profiel en hoog niveau; inclusief varianten zoals XDCAM, XDCAM HD, XDCAM IMX, CableLabs® en D10)
* MPEG-1
* Windows Media Video/VC-1
* JPEG-miniatuur maken
* HEVC (H. 265; 8-bits en 10-bits, hoofd-en hoofd-10-profiel)


### <a name="output-audio-codecs"></a>Audio-codecs voor uitvoer

* AES (SMPTE 331M en 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) tot 7,1
* AAC (AAC-LC, AAC-HE en AAC-HEv2; tot. 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio

>[!NOTE]
>Als u codeert naar Dolby® Digital (AC3), kan de uitvoer alleen naar een ISO MP4-bestand worden geschreven.

## <a name="support-for-closed-captioning"></a><a id="closed_captioning"></a>Ondersteuning voor ondertiteling

**Media Encoder Premium workflow** ondersteunt in opname:

1. SCC-bestanden
2. SMPTE-TT-bestanden
3. CEA-608/CEA-708: wordt als gebruikers gegevens (SEI berichten van H. 264-elementaire stromen, ATSC/53, SCTE20) of vervoerd als bijkomende gegevens in MXF/GXF-bestanden
4. STL-ondertitelings bestanden

Bij uitvoer zijn de volgende opties beschikbaar:

1. CEA-608 naar CEA-708-vertaling
2. CEA-608/CEA-708 Pass Through (Inge sloten in SEI berichten van H. 264 elementaire stromen of vervoerd als bijkomende gegevens in MXF-bestanden)
3. SCC
4. Getimede tekst van SMPTE (van bron CEA-608 per SMPTE RP2052, inclusief het maken van DFXP-bestanden)
5. Ondertitelings bestand SRT
6. DVB-ondertitel stromen

> [!NOTE]
> Niet alle hierboven genoemde uitvoer indelingen worden ondersteund voor levering via streaming in Azure Media Services.

## <a name="known-issues"></a>Bekende problemen

Als uw invoer video geen ondertiteling bevat, bevat het uitvoer activum nog een leeg TTML-bestand. 

## <a name="need-help"></a>Hulp nodig?

U kunt een ondersteunings ticket openen door te navigeren naar de [nieuwe ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
## <a name="media-services-learning-paths"></a>Media Services-leertrajecten

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

