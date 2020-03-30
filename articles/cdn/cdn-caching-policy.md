---
title: Azure CDN-cachingbeleid beheren in Azure Media Services | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u azure CDN-cachingbeleid beheert in Azure Media Services.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: dc0482fbcbb1c9d1618ec18e1f48b03f686a6573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74892573"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Azure CDN-cachingbeleid beheren in Azure Media Services
Azure Media Services biedt op HTTP gebaseerde Adaptive Streaming en progressieve download. HTTP-gebaseerde streaming is zeer schaalbaar met voordelen van caching in proxy- en CDN-lagen en caching aan de clientzijde. Streaming-eindpunten bieden algemene streamingmogelijkheden en ook configuratie voor HTTP-cacheheaders. Streaming endpoints sets HTTP Cache-Control: max-age en Expires headers. U meer informatie voor HTTP-cacheheaders krijgen van [W3.org.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)

## <a name="default-caching-headers"></a>Standaardcaching-kopteksten
Standaard passen streaming-endpoints 3-daagse cacheheaders toe voor on-demand streaminggegevens (werkelijke mediafragmenten/brokken) en manifest (afspeellijst). Voor live streaming passen streaming eindpunten 3-daagse cacheheaders toe voor gegevens (werkelijke mediafragmenten/brokken) en 2 seconden cacheheader voor manifest(playlist) verzoeken. Wanneer het live programma wordt ingeschakeld op on-demand (live archief), dan on-demand streaming cache headers van toepassing.

## <a name="azure-cdn-integration"></a>Azure CDN-integratie
Azure Media Services biedt [geïntegreerde CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) voor streaming-endpoints. Headers voor cachebeheer zijn van toepassing op dezelfde manier als streaming eindpunten naar cdn-streamingeindpunten. Azure CDN gebruikt streaming endpoint-geconfigureerde cachewaarden om de levensduur van de objecten in de interne cache te definiëren en gebruikt deze waarde ook om de kopteksten van de leveringscache in te stellen. Bij het gebruik van cdn-streamingeindpunten wordt het niet aanbevolen om kleine cachewaarden in te stellen. Het instellen van kleine waarden vermindert de prestaties en vermindert het voordeel van CDN. Het is niet toegestaan om cacheheaders in te stellen die kleiner zijn dan 600 seconden voor streaming-eindpunten met CDN.

> [!IMPORTANT]
>Azure Media Services heeft volledige integratie met Azure CDN. Met één klik u alle beschikbare Azure CDN-providers integreren in uw streaming-eindpunt, inclusief standaard- en premiumproducten. Zie voor meer informatie deze [aankondiging.](https://azure.microsoft.com/blog/standardstreamingendpoint/)
> 
> Gegevenskosten van streamingeindpunt naar CDN worden alleen uitgeschakeld als het CDN is ingeschakeld via streaming endpoint API's of de streaming endpoint-sectie van Azure portal gebruikt. Handmatige integratie of het rechtstreeks maken van een CDN-eindpunt met CDN-API's of portalsectie worden de gegevenskosten niet uitgeschakeld.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Cachekopteksten configureren met Azure Media Services
U Azure-portal of Azure Media Services-API's gebruiken om cachekopwaarden te configureren.

1. Als u cachekoppen wilt configureren met Azure-portal, raadpleegt u de sectie [Streaming Eindpunten beheren](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) Het eindpunt van streaming configureren.
2. Azure Media Services REST API, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [Eigenschappen van StreamingEndpointCacheControl](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Voorrangsvolgorde voor cacheconfiguratie
1. De geconfigureerde cachewaarde van Azure Media Services overschrijft de standaardwaarde.
2. Als er geen handmatige configuratie is, zijn standaardwaarden van toepassing.
3. Standaard zijn cacheheaders van 2 seconden van toepassing op live streaming manifest(afspeellijst) ongeacht azure media- of Azure-opslagconfiguratie en is het overschrijven van deze waarde niet beschikbaar.

