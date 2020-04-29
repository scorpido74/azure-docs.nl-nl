---
title: Azure Media Services verouderde onderdelen | Microsoft Docs
description: In dit onderwerp worden Azure Media Services oudere onderdelen beschreven.
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
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77921076"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services verouderde onderdelen

Na verloop van tijd zijn er stabiele verbeteringen en verbeteringen aangebracht in media service onderdelen. Als gevolg hiervan zijn sommige oudere onderdelen buiten gebruik gesteld. In de volgende artikelen vindt u de instructies voor het migreren van uw toepassing vanuit het verouderde onderdeel naar een actueel onderdeel.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Pensioen plannen van verouderde onderdelen en migratie richtlijnen

Er wordt een afschaffing van de media processors van *Windows Azure Media Encoder* (WAME) en *Azure Media Encoder* (AAM) aangekondigd. Deze processors worden op 31 maart 2020 buiten gebruik gesteld.

* [Migreren van Windows Azure Media Encoder naar Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migreren van Azure Media Encoder naar Media Encoder Standard](migrate-azure-media-encoder.md)

We kondigen ook de volgende Media Analytics-media processors in de buiten gebruiks telling: 
 
|Naam van mediaprocessor|Buitengebruikstellings datum|Aanvullende opmerkingen|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 januari, 2020|Deze media processor wordt vervangen door [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Zie [Migrate from Azure media indexer 2 to Azure Media Services video indexer](migrate-indexer-v1-v2.md)voor meer informatie.|
|[Azure Media Indexer](media-services-index-content.md)|1 maart 2023|Deze media processor wordt vervangen door [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Zie voor meer informatie [migreren van Azure media indexer naar Azure Media Services video indexer](migrate-indexer-v1-v2.md)|
|[Bewegings detectie](media-services-motion-detection.md)|1 juni, 2020|Er zijn op dit moment geen vervangings plannen.|
|[Video samenvatting](media-services-video-summarization.md)|1 juni, 2020|Er zijn op dit moment geen vervangings plannen.|
|[Video optische teken herkenning](media-services-video-optical-character-recognition.md)|1 juni, 2020|Deze media processor wordt vervangen door [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). U kunt ook [Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)gebruiken. <br/>Zie [Azure Media Services v3-voor instellingen en video indexer vergelijken](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Gezichtsherkenning](media-services-face-and-emotion-detection.md)|1 juni, 2020|Deze media processor wordt vervangen door [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). U kunt ook [Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)gebruiken. <br/>Zie [Azure Media Services v3-voor instellingen en video indexer vergelijken](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Content Moderator](media-services-content-moderation.md)|1 juni, 2020|Deze media processor wordt vervangen door [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). U kunt ook [Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)gebruiken. <br/>Zie [Azure Media Services v3-voor instellingen en video indexer vergelijken](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>Volgende stappen

[Migratie richtlijnen voor het overstappen van Media Services versie 2 naar v3](../latest/migrate-from-v2-to-v3.md)
