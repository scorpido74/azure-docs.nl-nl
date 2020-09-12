---
title: Migreren van Azure Media Services v2 naar v3 | Microsoft Docs
description: In dit artikel worden wijzigingen beschreven die in Azure Media Services v3 zijn geïntroduceerd en verschillen tussen twee versies worden weer gegeven. Het artikel bevat ook migratie richtlijnen voor het overstappen van Media Services v2 naar v3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, uitzenden, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b4e79a2aab5ca72ff8263bfc5734757bbff41005
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297729"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Migratie richtlijnen voor het overstappen van Media Services versie 2 naar v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>Ontvang een melding over wanneer u deze pagina voor updates opnieuw moet bezoeken door deze URL te kopiëren en te plakken: `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` in uw RSS-feed-lezer.

In dit artikel vindt u de richt lijnen voor migratie van Media Services versie 2 tot v3.

Als u op dit moment een video service hebt ontwikkeld voor de [oudere Media Services v2 api's](../previous/media-services-overview.md), moet u de volgende richt lijnen en overwegingen door nemen voordat u naar de V3-api's migreert. Er zijn veel voor delen en nieuwe functies in de V3 API die de ontwikkel ervaring en mogelijkheden van Media Services verbeteren. Net als in het gedeelte [bekende problemen](#known-issues) in dit artikel, zijn er echter ook enkele beperkingen door wijzigingen tussen de API-versies. Deze pagina blijft behouden naarmate het Media Services team voortdurende verbeteringen in de V3-Api's aanbrengt en de hiaten tussen de versies verhelpt. 

## <a name="prerequisites"></a>Vereisten

* Controleer [Media Services v2 versus v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Voor delen van Media Services v3
  
### <a name="api-is-more-approachable"></a>API is beter te benaderen

*  v3 is gebaseerd op een geïntegreerde API-gebied dat functionaliteit voor zowel beheer als bewerkingen beschikbaar stelt die is gebouwd op Azure Resource Manager. Azure Resource Manager sjablonen kunnen worden gebruikt voor het maken en implementeren van trans formaties, streaming-eind punten, Live-gebeurtenissen en meer.
* [OpenAPI-specificatie (voorheen Swagger-document genoemd)](https://aka.ms/ams-v3-rest-sdk) .
    Beschrijft het schema voor alle service onderdelen, inclusief code ring op basis van een bestand.
* Sdk's beschikbaar voor [.net](https://aka.ms/ams-v3-dotnet-ref), .net Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref)en Ruby.
* [Azure cli](https://aka.ms/ams-v3-cli-ref) -integratie voor eenvoudige script ondersteuning.

### <a name="new-features"></a>Nieuwe functies

* Voor taak verwerking op basis van een bestand kunt u een HTTP/S-URL gebruiken als invoer.<br/>U hoeft inhoud niet al in azure te hebben opgeslagen en u hoeft geen assets te maken.
* Introduceert het concept van [trans formaties](transforms-jobs-concept.md) voor taak verwerking op basis van bestanden. Een trans formatie kan worden gebruikt om herbruikbare configuraties te bouwen, Azure Resource Manager-sjablonen te maken en verwerkings instellingen te isoleren tussen meerdere klanten of tenants.
* Een Asset kan meerdere [Stream-Locators](streaming-locators-concept.md) hebben elk met verschillende [dynamische pakketten](dynamic-packaging-overview.md) en dynamische versleutelings instellingen.
* [Content Protection](content-key-policy-concept.md) ondersteunt functies met meerdere sleutels.
* U kunt live-gebeurtenissen die Maxi maal 24 uur duren, streamen wanneer u Media Services gebruikt voor het omzetten van een single bitrate-bijdrage-feed in een uitvoer stroom met meerdere bitrates.
* Nieuwe ondersteuning voor live streaming met lage latentie voor Live-gebeurtenissen. Zie [latentie](live-event-latency.md)voor meer informatie.
* Preview van Live Event ondersteunt [dynamische pakketten](dynamic-packaging-overview.md) en dynamische versleuteling. Hiermee wordt de beveiliging van inhoud op de preview-versie en de verstreep-en HLS-verpakking ingeschakeld.
* Live output is eenvoudiger te gebruiken dan de entiteit van het programma in de v2-Api's. 
* Verbeterde RTMP-ondersteuning (verbeterde stabiliteit en meer ondersteuning voor broncode encoders).
* RTMP beveiligde opname.<br/>Wanneer u een live gebeurtenis maakt, krijgt u vier opname-Url's. De 4 opname-Url's zijn bijna identiek, hebben hetzelfde streaming token (AppId), alleen het poort nummer onderdeel is anders. Twee van de Url's zijn primaire en back-ups voor RTMP.   
* U hebt op rollen gebaseerd toegangs beheer (RBAC) voor uw entiteiten. 

## <a name="known-issues"></a>Bekende problemen

*  Op dit moment kunt u het [Azure Portal](https://portal.azure.com/) gebruiken voor het volgende:

    * Media Services v3 [Live-gebeurtenissen](live-events-outputs-concept.md)beheren, 
    * V3- [assets](assets-concept.md)weer geven (niet beheren), 
    * [krijg informatie over het openen van api's](./access-api-howto.md). 

    Gebruik voor alle andere beheertaken (bijvoorbeeld [transformaties en taken](transforms-jobs-concept.md) en [inhoudsbeveiliging](content-protection-overview.md)), de [REST API](/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref) of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).
* U moet gereserveerde media-eenheden (MRUs) inrichten in uw account om de gelijktijdigheid en prestaties van uw taken te bepalen, met name voor de analyse van video of audio. Zie [Mediaverwerking schalen](../previous/media-services-scale-media-processing-overview.md) voor meer informatie. U kunt de MRUs beheren met [CLI 2,0 voor Media Services v3](media-reserved-units-cli-how-to.md), met behulp van de [Azure Portal](../previous/media-services-portal-scale-media-processing.md)of met behulp van de [v2-api's](../previous/media-services-dotnet-encoding-units.md). U moet MRUs inrichten, ongeacht of u Media Services v2-of v3-Api's gebruikt.
* Media Services entiteiten die met de V3 API zijn gemaakt, kunnen niet worden beheerd door de v2 API.  
* Niet alle entiteiten in de v2 API worden automatisch weer gegeven in de V3 API.  Hieronder volgen enkele voor beelden van entiteiten in de twee versies die niet compatibel zijn:  
    * Taken en taken die zijn gemaakt in v2, worden niet weer gegeven in v3, omdat ze niet zijn gekoppeld aan een trans formatie. De aanbeveling is om over te scha kelen naar v3-trans formaties en-taken. Er is een relatief korte periode voor het bewaken van de Inflight v2-taken tijdens het overschakelen.
    * Kanalen en Program Ma's die zijn gemaakt met v2 (die zijn toegewezen aan Live-gebeurtenissen en live-uitvoer in v3), kunnen niet worden beheerd met v3. De aanbeveling is om over te scha kelen naar v3 Live-gebeurtenissen en live-uitvoer op een handige kanaal stop.<br/>Momenteel kunt u continu actieve kanalen niet migreren.  

> [!NOTE]
> Deze pagina blijft behouden naarmate het Media Services team voortdurende verbeteringen in de V3-Api's aanbrengt en de hiaten tussen de versies verhelpt.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: Extern bestand coderen op basis van URL en video streamen - .NET](stream-files-dotnet-quickstart.md)
