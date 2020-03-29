---
title: Verouderde onderdelen van Azure Media Services | Microsoft Documenten
description: In dit onderwerp worden verouderde onderdelen van Azure Media Services besproken.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921076"
---
# <a name="azure-media-services-legacy-components"></a>Verouderde onderdelen van Azure Media Services

In de loop der tijd zijn er gestage verbeteringen en verbeteringen aan Media Service-componenten. Als gevolg hiervan zijn sommige verouderde onderdelen buiten gebruik gesteld. In de volgende artikelen vindt u de instructies voor het migreren van uw toepassing van de verouderde component naar een huidige component.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Pensioenplannen van oudere componenten en migratiebegeleiding

We kondigen de afschaffing aan van de *Media-processors van Windows Azure Media Encoder* (WAME) en Azure Media *Encoder* (AME). Deze processors worden op 31 maart 2020 met pensioen.

* [Migreren van Windows Azure Media Encoder naar Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migreren van Azure Media Encoder naar Media Encoder Standard](migrate-azure-media-encoder.md)

We kondigen ook het pensioen aan van de volgende Media Analytics-mediaprocessors: 
 
|Naam van mediaprocessor|Pensioendatum|Aanvullende opmerkingen|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 januari 2020|Deze mediaprocessor wordt vervangen door [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Zie [Migreren van Azure Media Indexer 2 naar Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)voor meer informatie.|
|[Azure Media Indexer](media-services-index-content.md)|1 maart 2023|Deze mediaprocessor wordt vervangen door [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Zie [Migreren van Azure Media Indexer naar Video-indexer van Azure Media Services voor](migrate-indexer-v1-v2.md) meer informatie|
|[Bewegingsdetectie](media-services-motion-detection.md)|1 juni 2020|Geen vervangingsplannen op dit moment.|
|[Video Summarisatie](media-services-video-summarization.md)|1 juni 2020|Geen vervangingsplannen op dit moment.|
|[Optische tekenherkenning van video](media-services-video-optical-character-recognition.md)|1 juni 2020|Deze mediaprocessor wordt vervangen door [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Overweeg ook [om Azure Media Services v3 API te gebruiken.](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept) <br/>Zie [Azure Media Services v3-voorinstellingen en video-indexer vergelijken](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Gezichtsherkenning](media-services-face-and-emotion-detection.md)|1 juni 2020|Deze mediaprocessor wordt vervangen door [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Overweeg ook [om Azure Media Services v3 API te gebruiken.](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept) <br/>Zie [Azure Media Services v3-voorinstellingen en video-indexer vergelijken](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Content Moderator](media-services-content-moderation.md)|1 juni 2020|Deze mediaprocessor wordt vervangen door [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Overweeg ook [om Azure Media Services v3 API te gebruiken.](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept) <br/>Zie [Azure Media Services v3-voorinstellingen en video-indexer vergelijken](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>Volgende stappen

[Migratierichtlijnen voor de overgang van Media Services v2 naar v3](../latest/migrate-from-v2-to-v3.md)
