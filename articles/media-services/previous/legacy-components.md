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
ms.date: 10/27/2019
ms.author: juliako
ms.openlocfilehash: 30e8fa51df42d202d77ecdbc6a31fe3c7aaac6f4
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968629"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services verouderde onderdelen

Na verloop van tijd zijn er stabiele verbeteringen en verbeteringen aangebracht in media service onderdelen. Als gevolg hiervan zijn sommige oudere onderdelen buiten gebruik gesteld. In de volgende artikelen vindt u de instructies voor het migreren van uw toepassing vanuit het verouderde onderdeel naar een actueel onderdeel.

## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Pensioen plannen van verouderde onderdelen en migratie richtlijnen

Er wordt een afschaffing van de media processors van *Windows Azure Media Encoder* (WAME) en *Azure Media Encoder* (AAM) aangekondigd. Deze processors worden op 30 november 2019 buiten gebruik gesteld.

* [Migreren van Windows Azure Media Encoder naar Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migreren van Azure Media Encoder naar Media Encoder Standard](migrate-azure-media-encoder.md)

We kondigen ook de volgende Media Analytics-media processors in de buiten gebruiks telling: 

|Naam van mediaprocessor|Buitengebruikstellings datum|Aanvullende opmerkingen|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1 januari van 2020|Deze media processor wordt vervangen door [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Zie [Migrate from Azure media indexer 2 to Azure Media Services video indexer](migrate-indexer-v1-v2.md)voor meer informatie.|
|[Azure Media Indexer](media-services-index-content.md)|1 oktober van 2020|Deze media processor wordt vervangen door [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Zie voor meer informatie [migreren van Azure media indexer naar Azure Media Services video indexer](migrate-indexer-v1-v2.md)
|[Azure Media Face Detector](media-services-face-and-emotion-detection.md)|1 februari 2020|Deze Media Analytics preview-processor wordt buiten gebruik gesteld en wordt niet verplaatst naar de algemene Beschik baarheid. We zullen de scenario's en gebruiks voorbeelden met klanten evalueren voor toekomstige investeringen.|
|[Azure Media Motion Detector](media-services-motion-detection.md)|1 februari 2020|Deze Media Analytics preview-processor wordt buiten gebruik gesteld en wordt niet verplaatst naar de algemene Beschik baarheid. We zullen de scenario's en gebruiks voorbeelden met klanten evalueren voor toekomstige investeringen.|
|[Azure media OCR](media-services-video-optical-character-recognition.md)|1 februari 2020|Deze media processor wordt vervangen door [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) en de [Azure Media Services v3 API video Analyzer vooraf](../latest/analyzing-video-audio-files-concept.md).|
|[Azure Media Video Thumbnails](media-services-video-summarization.md)|1 februari 2020|Deze media processor wordt vervangen door [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) en de [Azure Media Services v3 API video Analyzer vooraf](../latest/analyzing-video-audio-files-concept.md).|

## <a name="next-steps"></a>Volgende stappen

[Migratie richtlijnen voor het overstappen van Media Services versie 2 naar v3](../latest/migrate-from-v2-to-v3.md)
