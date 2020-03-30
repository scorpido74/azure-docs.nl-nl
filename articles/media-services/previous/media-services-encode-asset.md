---
title: Overzicht van Azure on-demand media-encoders | Microsoft Documenten
description: Azure Media Services biedt meerdere opties voor het coderen van media in de cloud. Dit artikel geeft een overzicht van Azure on-demand media-encoders.
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
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: d5d5b8a7328ee82e94d494795617832cb0258667
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251100"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Overzicht van Azure on-demand media-encoders 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Azure Media Services biedt meerdere opties voor het coderen van media in de cloud.

Wanneer u begint met Media Services, is het belangrijk om het verschil tussen codecs en bestandsindelingen te begrijpen.
Codecs zijn de software die de compressie-/decompressiealgoritmen implementeert, terwijl bestandsindelingen containers zijn die de gecomprimeerde video bevatten.

Media Services biedt dynamische verpakkingen waarmee u uw adaptieve bitrate MP4- of Smooth Streaming-gecodeerde inhoud leveren in streamingformaten die worden ondersteund door Media Services (MPEG DASH, HLS, Smooth Streaming) zonder dat u opnieuw hoeft te verpakken in deze streamingformaten.

Wanneer uw Media Services-account wordt gemaakt, wordt een **standaard** streamingeindpunt toegevoegd aan uw account in de status **Gestopt.** Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. Facturering voor streamingeindpunten vindt plaats wanneer het eindpunt in **een status actief** is.

Media Services ondersteunt het volgende on demand-encoders die in dit artikel worden beschreven:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium Workflow](media-services-encode-asset.md#media-encoder-premium-workflow)

Dit artikel geeft een kort overzicht van on demand media encoders en biedt links naar artikelen die meer gedetailleerde informatie geven. Het onderwerp biedt ook een vergelijking van de encoders.

Standaard kan elk Media Services-account één actieve coderingstaak tegelijk hebben. U coderingseenheden reserveren waarmee u meerdere coderingstaken tegelijk uitvoeren, één voor elke coderingsreservedie u koopt. Zie [Coderingseenheden schalen](media-services-scale-media-processing-overview.md)voor informatie .

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Gebruiksinstructies
[Coderen met Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Indelingen
[Indelingen en codecs](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Voorinstellingen
Media Encoder Standard is geconfigureerd met behulp van een van de encoder presets [hier](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)beschreven.

### <a name="input-and-output-metadata"></a>Metagegevens voor invoer en uitvoer
De encoders invoer metadata wordt [hier](media-services-input-metadata-schema.md)beschreven .

De metagegevens van de encoders-uitvoer worden [hier](media-services-output-metadata-schema.md)beschreven.

### <a name="generate-thumbnails"></a>Miniaturen genereren
Zie [Miniaturen genereren met Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails)voor meer informatie.

### <a name="trim-videos-clipping"></a>Video's bijsnijden (knippen)
Zie [Video's bijsnijden met Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video)voor meer informatie.

### <a name="create-overlays"></a>Overlays maken
Zie [Overlays maken met Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay)voor meer informatie.

### <a name="see-also"></a>Zie ook
[De Media Services blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Media Encoder Premium Workflow
### <a name="overview"></a>Overzicht
[Premium-codering introduceren in Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Gebruiksinstructies
Media Encoder Premium Workflow is geconfigureerd met behulp van complexe workflows. Werkstroombestanden kunnen worden gemaakt en bijgewerkt met het gereedschap [Workflow Designer.](media-services-workflow-designer.md)

[Premium-codering gebruiken in Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Bekende problemen
Als uw invoervideo geen ondertiteling bevat, bevat het uitvoerelement nog steeds een leeg TTML-bestand.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Verwante artikelen:
* [Geavanceerde coderingstaken uitvoeren door voorinstellingen van Media Encoder Standard aan te werken](media-services-custom-mes-presets-with-dotnet.md)
* [Quota en beperkingen](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
