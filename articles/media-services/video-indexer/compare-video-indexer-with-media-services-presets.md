---
title: Vergelijking van V3-voorinstellingen voor VideoIndexer en Azure Media Services
description: In dit artikel worden de mogelijkheden van Video Indexer en de V3-voorinstellingen van Azure Media Services vergeleken.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: dcfc6ea4afe23424e72c625518356be52f62bc81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602184"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Vergelijk Azure Media Services v3-voorinstellingen en Video-indexer 

In dit artikel worden de mogelijkheden van **Video Indexer API's** en **Media Services v3 API's vergeleken.** 

Momenteel is er een overlap tussen functies die worden aangeboden door de [Video Indexer API's](https://api-portal.videoindexer.ai/) en de [Media Services v3 API's](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). De volgende tabel biedt de huidige richtlijn voor het begrijpen van de verschillen en overeenkomsten. 

## <a name="compare"></a>Vergelijken

|Functie|Video Indexer API's |Voorinstellingen voor video-analyzer en audioanalyse<br/>in Media Services v3 API's|
|---|---|---|
|Media-inzichten|[Verbeterd](video-indexer-output-json-v2.md) |[Basisprincipes](../latest/intelligence-concept.md)|
|Ervaringen|Bekijk de volledige lijst met ondersteunde functies: <br/> [Overzicht](video-indexer-overview.md)|Geeft alleen als resultaat video-inzichten|
|Billing|[Prijzen mediaservices](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Prijzen mediaservices](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Naleving|Ga voor de meest recente nalevingsupdates naar [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) en zoek naar 'Video Indexer' om te zien of deze voldoet aan een certificaat van belang.|Ga voor de meest recente nalevingsupdates naar [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) en zoek naar 'Media Services' om te zien of het voldoet aan een certificaat van belang.|
|Gratis proefversie|VS - oost|Niet beschikbaar|
|Beschikbaarheid in regio’s|Beschikbaarheid [van cognitieve services per regio bekijken](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Zie [Beschikbaarheid mediaservices per regio](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)

[Media Services v3 overzicht](../latest/media-services-overview.md)
