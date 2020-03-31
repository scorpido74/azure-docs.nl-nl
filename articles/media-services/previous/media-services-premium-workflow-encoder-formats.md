---
title: Media Encoder Premium Workflow-indelingen en codecs | Microsoft Documenten
description: Dit onderwerp geeft een overzicht van Media Encoder Premium Workflow Formats formats en codecs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269755"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Indelingen en codecs voor Media Encoder Premium Workflow

> [!NOTE]
> Media Encoder Premium Workflow media processor besproken in dit onderwerp is niet beschikbaar in China. 

Dit document bevat een lijst met invoer- en uitvoerbestandsindelingen en codecs die worden ondersteund door de openbare preview-versie van de **media-encoder** premiumworkflow-encoder.

[Media Encoder Premium Workflow-invoerindelingen en -codecs](#input_formats)

Media Encoder Premium Workflow Output Formats en Codecs

**Media Encoder Premium Workflow** ondersteunt ondertiteling die in [deze](#closed_captioning) sectie wordt beschreven. 

## <a name="media-encoder-premium-workflow-input-formats-and-codecs"></a><a id="input_formats"></a>Media Encoder Premium Workflow-invoerindelingen en -codecs

In de volgende sectie worden de codecs en bestandsindelingen weergegeven die deze mediaprocessor als invoer ondersteunt.

### <a name="input-containerfile-formats"></a>Invoercontainer/bestandsindelingen

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF GXF
* MPEG-2 transportstromen
* MPEG-2-programmastreams
* MPEG-4/MP4
* Windows Media/ASF
* AVI (Niet gecomprimeerde 8bit/10bit)

### <a name="input-video-codecs"></a>Codecs invoervideo

* AVC 8-bits/10-bits, maximaal 4:2:2, inclusief AVCIntra
* Avid DNxHD (in MXF)
* DVCPro/DVCProHD (in MXF)
* HEVC/H.265, Hoofd- en Hoofdprofiel 10
* JPEG2000
* MPEG-2 (tot 422 profiel en hoog niveau; inclusief varianten zoals XDCAM, XDCAM HD, XDCAM IMX, CableLabs® en D10)
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

## <a name="media-encoder-premium-workflow-output-formats-and-codecs"></a><a id="output_format"></a>Media Encoder Premium Workflow Output Formats en Codecs

In de volgende sectie worden de codecs en bestandsindelingen weergegeven die worden ondersteund als uitvoer vanuit deze mediaprocessor.

### <a name="output-containerfile-formats"></a>Uitvoercontainer/bestandsindelingen

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM en AS02)
* DPP (inclusief AS11)
* GXF GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (Niet gecomprimeerde 8bit/10bit)
* Vloeiende streamingbestandsindeling (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Videocodecs uitvoeren

* AVC (H.264; 8-bit; tot High Profile, Level 5.2; 4K Ultra HD; AVC Intra)
* Avid DNxHD (in MXF)
* DVCPro/DVCProHD (in MXF)
* MPEG-2 (tot 422 profiel en hoog niveau; inclusief varianten zoals XDCAM, XDCAM HD, XDCAM IMX, CableLabs® en D10)
* MPEG-1
* Windows Media Video/VC-1
* JPEG-miniatuurmaken
* HEVC (H.265; 8 bit en 10 bit, Main en Main 10 Profiel)


### <a name="output-audio-codecs"></a>Audiocodecs uitvoeren

* AES (SMPTE 331M en 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) tot 7,1
* AAC (AAC-LC, AAC-HE en AAC-HEv2; tot. 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio

>[!NOTE]
>Als u codeert naar Dolby® Digital (AC3), kan de uitvoer alleen in een ISO MP4-bestand worden geschreven.

## <a name="support-for-closed-captioning"></a><a id="closed_captioning"></a>Ondersteuning voor ondertiteling

Media **Encoder Premium Workflow** ondersteunt bij inname:

1. SCC-bestanden
2. SMPTE-TT-bestanden
3. CEA-608/CEA-708 – als gebruikersgegevens (SEI-berichten van H.264 elementaire streams, ATSC/53, SCTE20) of als bijkomende gegevens in MXF/GXF-bestanden
4. STL ondertitelbestanden

Bij uitvoer zijn de volgende opties beschikbaar:

1. CEA-608 naar CEA-708 vertaling
2. CEA-608/CEA-708 passeren (ingebed in SEI-berichten van H.264 elementaire streams, of vervoerd als bijkomende gegevens in MXF-bestanden)
3. Scc
4. Getimede SMPTE-tekst (van bron CEA-608 per SMPTE RP2052; inclusief DFXP-bestandscreatie)
5. SRT-ondertitelbestand
6. DVB ondertitel streams

> [!NOTE]
> Niet alle bovenstaande uitvoerindelingen worden ondersteund voor levering via streaming in Azure Media Services.

## <a name="known-issues"></a>Bekende problemen

Als uw invoervideo geen ondertiteling bevat, bevat het uitvoerelement nog steeds een leeg TTML-bestand. 

## <a name="need-help"></a>Hebt u hulp nodig?

U een ondersteuningsticket openen door te navigeren naar [nieuw ondersteuningsverzoek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
## <a name="media-services-learning-paths"></a>Media Services-leertrajecten

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

