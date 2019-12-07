---
title: Vergelijking van Video Indexer en Azure Media Services voorinstellingen voor v3 | Microsoft Docs
description: In dit artikel worden Video Indexer mogelijkheden en Azure Media Services v3-voor waarden vergeleken.
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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 055e210efbb01268654e7823a0fc1320e9c27646
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892818"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Azure Media Services voorinstellingen voor v3 en Video Indexer vergelijken 

In dit artikel worden de mogelijkheden van **video indexer api's** en **Media Services v3 api's**vergeleken. 

Er is momenteel een overlap ping tussen de functies die worden geboden door de [video indexer api's](https://api-portal.videoindexer.ai/) en de [Media Services v3-api's](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). De volgende tabel bevat de huidige richt lijnen voor het leren van de verschillen en overeenkomsten. 

## <a name="compare"></a>Vergelijken

|Functie|Video Indexer-Api's |Video-en audio analyse-voor instellingen<br/>in Media Services v3-Api's|
|---|---|---|
|Media Insights|[Intensievere](video-indexer-output-json-v2.md) |[Basisprincipes](../latest/intelligence-concept.md)|
|Bewerkingen|Bekijk de volledige lijst met ondersteunde functies: <br/> [Overzicht](video-indexer-overview.md)|Retourneert alleen video inzichten|
|Billing|[Media Services prijzen](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Media Services prijzen](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Naleving|[Iso 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1, 2, 3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci)en [HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) gecertificeerd. Voor de meest recente updates gaat u naar de [status van de huidige certificeringen van video indexer](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Media Services voldoet aan een groot aantal certificeringen. Bekijk de [Azure compliance-aanbiedingen. PDF](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) en zoek naar "Media Services" om te zien of het voldoet aan een certificaat van belang.|
|Gratis proefversie|VS - oost|Niet beschikbaar|
|Regionale beschikbaarheid|VS-Oost 2, Zuid-Centraal VS, VS-West 2, Europa-noord, Europa-west, Zuidoost-Azië, Azië-oost en Australië-oost.  Voor de meest recente updates gaat u naar de pagina [producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) .|Zie de [Azure-status](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)

[Overzicht van Media Services v3](../latest/media-services-overview.md)
