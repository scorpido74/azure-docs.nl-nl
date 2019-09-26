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
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: d4f4cfc005b2d5a63512245baee5230e9a26cb37
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309212"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services verouderde onderdelen

Na verloop van tijd zijn er stabiele verbeteringen en verbeteringen aangebracht in media service onderdelen. Als gevolg hiervan zijn sommige oudere onderdelen buiten gebruik gesteld. In de volgende artikelen vindt u de instructies voor het migreren van uw toepassing vanuit het verouderde onderdeel naar een actueel onderdeel.

## <a name="legacy-components-and-migration-guidance"></a>Verouderde onderdelen en migratie richtlijnen

Er wordt een afschaffing van de media processors van *Windows Azure Media Encoder* (WAME) en *Azure Media Encoder* (AAM) aangekondigd. Deze processors worden op 30 november 2019 buiten gebruik gesteld.

* [Migreren van Windows Azure Media Encoder naar Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migreren van Azure Media Encoder naar Media Encoder Standard](migrate-azure-media-encoder.md)

We kondigen ook de afschaffing van *Azure media indexer* en *Azure media indexer 2*. De processor van [Azure media indexer](media-services-index-content.md) media wordt op 1 oktober 2020 buiten gebruik gesteld. De media processors van [[Azure media indexer 2 (preview)](media-services-process-content-with-indexer2.md) worden buiten gebruik gesteld op 1 januari van 2019.  [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) vervangt deze verouderde media processors.

* [Migreer van Azure media indexer en Azure media indexer 2 naar Azure Media Services video indexer](migrate-indexer-v1-v2.md).

## <a name="next-steps"></a>Volgende stappen

[Migratie richtlijnen voor het overstappen van Media Services versie 2 naar v3](../latest/migrate-from-v2-to-v3.md)
