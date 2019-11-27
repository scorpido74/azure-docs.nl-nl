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
ms.date: 11/22/2019
ms.author: juliako
ms.openlocfilehash: 6a61c868da7b979b1ac5654f4be93b5245179105
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423927"
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
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1 januari van 2020|Deze media processor wordt vervangen door [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Zie [Migrate from Azure media indexer 2 to Azure Media Services video indexer](migrate-indexer-v1-v2.md)voor meer informatie.|
|[Azure Media Indexer](media-services-index-content.md)|1 oktober van 2020|Deze media processor wordt vervangen door [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Zie voor meer informatie [migreren van Azure media indexer naar Azure Media Services video indexer](migrate-indexer-v1-v2.md)

## <a name="next-steps"></a>Volgende stappen

[Migratie richtlijnen voor het overstappen van Media Services versie 2 naar v3](../latest/migrate-from-v2-to-v3.md)
