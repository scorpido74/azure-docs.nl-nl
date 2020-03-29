---
title: Overzicht van dynamische verpakkingen van Azure Media Services | Microsoft Documenten
description: Deze artikelen geven een overzicht van de dynamische verpakking van Microsoft Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: 079094965775c140c0343da98e40fd008995d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901191"
---
# <a name="dynamic-packaging"></a>Dynamische verpakking

> [!div class="op_single_selector" title1="Selecteer de versie van Media Services die u gebruikt:"]
> * [Versie 3](../latest/dynamic-packaging-overview.md)
> * [Versie 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure Media Services kan worden gebruikt om veel bestandsindelingen voor mediabronnen, mediastreaming-indelingen en indelingen voor inhoudsbescherming te leveren aan verschillende clienttechnologieën (bijvoorbeeld iOS, XBOX, Silverlight, Windows 8). Deze clients begrijpen verschillende protocollen, bijvoorbeeld iOS vereist een HTTP Live Streaming (HLS) V4-formaat en Silverlight en Xbox vereisen Smooth Streaming. Als u een set adaptieve bitrate (multi-bitrate) MP4 (ISO Base Media 14496-12) bestanden of een set adaptieve bitrate Smooth Streaming-bestanden hebt die u wilt bedienen aan clients die MPEG DASH, HLS of Smooth Streaming begrijpen, moet u profiteren van Media Diensten dynamische verpakking.

Met dynamische verpakking alles, moet u een asset die een set adaptieve bitrate MP4-bestanden of adaptieve bitrate Smooth Streaming bestanden bevat. Vervolgens zorgt de on-demand streamingserver, op basis van de opgegeven indeling in het manifest- of fragmentverzoek, ervoor dat u de stream ontvangt in het door u gekozen protocol. Hierdoor hoeft u voor slechts één opslagindeling de bestanden op te slaan en hiervoor te betalen. De Media Services-service bouwt en levert de juiste reactie op basis van aanvragen van een client.

Het volgende diagram toont de traditionele coderings- en statische verpakkingsworkflow.

![Statische codering](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

In het volgende diagram ziet u de dynamische verpakkingsworkflow.

![Dynamische codering](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Algemeen scenario

1. Upload een invoerbestand (een mezzaninebestand genoemd). H.264, MP4 of WMV (zie Bijvoorbeeld Formaten die [worden ondersteund door de Media Encoder-standaard](media-services-media-encoder-standard-formats.md).
2. Codeer uw mezzaninebestand op H.264 MP4 adaptieve bitrate-sets.
3. Publiceer het item dat de adaptieve bitrate MP4-set bevat door de on-demand locator te maken.
4. Bouw de streaming-URL's om toegang te krijgen tot en te streamen.

## <a name="preparing-assets-for-dynamic-streaming"></a>Activa voorbereiden op dynamische streaming

Om uw asset voor te bereiden op dynamische streaming, hebt u de volgende opties:

- [Een hoofdbestand uploaden](media-services-dotnet-upload-files.md).
- [Gebruik de Media Encoder Standard-encoder om H.264 MP4 adaptieve bitrate-sets te produceren.](media-services-dotnet-encode-with-media-encoder-standard.md)
- [Stream je inhoud](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Audiocodecs ondersteund door dynamische verpakking

Dynamic Packaging ondersteunt MP4-bestanden, die audio bevatten die zijn gecodeerd met [OC (AAC-LC,](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) HE-AAC v1, HE-AAC v2), Dolby Digital [Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 of E-AC3), Dolby Atmos of [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD Lossless). Streaming van Dolby Atmos-inhoud wordt ondersteund voor standaarden zoals mpeg-DASH-protocol met common streaming format (CSF) of Common Media Application Format (CMAF) gefragmenteerde MP4, en via HTTP Live Streaming (HLS) met CMAF.

> [!NOTE]
> Dynamic Packaging ondersteunt geen bestanden die [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) audio bevatten (het is een legacy codec).

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

