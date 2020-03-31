---
title: Migreren van Azure Media Services v2 naar v3 | Microsoft Documenten
description: In dit artikel worden wijzigingen beschreven die zijn geïntroduceerd in Azure Media Services v3 en worden verschillen tussen twee versies weergegeven. Het artikel biedt ook migratierichtlijnen voor het verplaatsen van Media Services v2 naar v3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, uitzenden, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 72d413c5d8bc982d885d889da35b29a3607410cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472064"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Migratierichtlijnen voor de overgang van Media Services v2 naar v3

>Ontvang een melding over wanneer u deze pagina opnieuw moet `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` bezoeken voor updates door deze URL te kopiëren en te plakken: in uw RSS-feedlezer.

Dit artikel biedt de migratiebegeleiding van Media Services v2 naar v3.

Als u een videoservice hebt die vandaag is ontwikkeld bovenop de [verouderde Media Services v2-API's,](../previous/media-services-overview.md)moet u de volgende richtlijnen en overwegingen bekijken voordat u naar de v3-API's migreert. Er zijn veel voordelen en nieuwe functies in de v3-API die de ontwikkelaarservaring en -mogelijkheden van Media Services verbeteren. Echter, zoals genoemd in de [bekende problemen](#known-issues) sectie van dit artikel, zijn er ook enkele beperkingen als gevolg van wijzigingen tussen de API-versies. Deze pagina zal worden onderhouden als de Media Services team maakt voortdurende verbeteringen aan de v3 API's en pakt de hiaten tussen de versies. 

## <a name="prerequisites"></a>Vereisten

* Bekijk [Media Services v2 vs. v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Voordelen van Media Services v3
  
### <a name="api-is-more-approachable"></a>API is toegankelijker

*  v3 is gebaseerd op een geïntegreerde API-gebied dat functionaliteit voor zowel beheer als bewerkingen beschikbaar stelt die is gebouwd op Azure Resource Manager. Azure Resource Manager-sjablonen kunnen worden gebruikt voor het maken en implementeren van transformaties, streamingeindpunten, livegebeurtenissen en meer.
* [OpenAPI Specification (voorheen Swagger genoemd)](https://aka.ms/ams-v3-rest-sdk) document.
    Hiermee wordt het schema voor alle servicecomponenten, inclusief bestandscodering, openbaar.
* SDKs beschikbaar voor [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref)en Ruby.
* [Azure CLI-integratie](https://aka.ms/ams-v3-cli-ref) voor eenvoudige ondersteuning voor scripting.

### <a name="new-features"></a>Nieuwe functies

* Voor taakverwerking op basis van bestanden u een HTTP(S)-URL als invoer gebruiken.<br/>U hoeft geen inhoud te hebben die al in Azure is opgeslagen en u hoeft ook geen assets te maken.
* Introduceert het concept [van Transformaties](transforms-jobs-concept.md) voor bestandsgebaseerde taakverwerking. Een transformatie kan worden gebruikt om herbruikbare configuraties te maken, Azure Resource Manager-sjablonen te maken en verwerkingsinstellingen tussen meerdere klanten of tenants te isoleren.
* Een asset kan meerdere [Streaming Locators](streaming-locators-concept.md) hebben met verschillende dynamische [verpakkings-](dynamic-packaging-overview.md) en dynamische versleutelingsinstellingen.
* [Contentbescherming](content-key-policy-concept.md) ondersteunt multi-key functies.
* U live gebeurtenissen streamen die maximaal 24 uur duren wanneer u Media Services gebruikt voor het transcoderen van een enkele bitrate bijdragefeed in een uitvoerstream met meerdere bitrates.
* Nieuwe Low Latency live streaming ondersteuning op Live Events. Zie [latentie voor](live-event-latency.md)meer informatie.
* Live Event Preview ondersteunt [Dynamic Packaging](dynamic-packaging-overview.md) en Dynamic Encryption. Dit maakt contentbescherming op Preview en DASH- en HLS-verpakkingen mogelijk.
* Live Output is eenvoudiger te gebruiken dan de entiteit Programma in de v2-API's. 
* Verbeterde RTMP-ondersteuning (meer stabiliteit en meer ondersteuning voor bronencoder).
* RTMPS veilig inname.<br/>Wanneer u een Live Event maakt, krijgt u 4 inname-URL's. De 4 inname URL's zijn bijna identiek, hebben dezelfde streaming token (AppId), alleen het poortnummer deel is anders. Twee van de URL's zijn primair en back-up voor RTMPS.   
* U hebt op rollen gebaseerd toegangscontrole (RBAC) ten opzichte van uw entiteiten. 

## <a name="known-issues"></a>Bekende problemen

*  Momenteel u de [Azure-portal](https://portal.azure.com/) gebruiken om:

    * Media Services v3 [Live Events](live-events-outputs-concept.md)beheren, 
    * bekijken (niet beheren) v3 [Activa](assets-concept.md), 
    * [informatie krijgen over toegang tot API's](access-api-portal.md). 

    Voor alle andere beheertaken (bijvoorbeeld [Transformaties en Taken](transforms-jobs-concept.md) en [Contentbescherming),](content-protection-overview.md)gebruikt u de [REST API](https://docs.microsoft.com/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).
* U moet Media Reserved Units (MRU's) in uw account inrichten om de gelijktijdigheid en prestaties van uw taken te controleren, met name die met betrekking tot video- of audioanalyse. Zie [Mediaverwerking schalen](../previous/media-services-scale-media-processing-overview.md) voor meer informatie. U de MRU's beheren met [CLI 2.0 voor Media Services v3,](media-reserved-units-cli-how-to.md)via de [Azure-portal](../previous/media-services-portal-scale-media-processing.md)of met behulp van de [v2-API's](../previous/media-services-dotnet-encoding-units.md). U moet MRI's inleveren, of u nu Media Services v2- of v3-API's gebruikt.
* Media Services-entiteiten die met de v3-API zijn gemaakt, kunnen niet worden beheerd door de v2-API.  
* Niet alle entiteiten in de V2-API worden automatisch weergegeven in de V3-API.  Hieronder volgen voorbeelden van entiteiten in de twee versies die onverenigbaar zijn:  
    * Taken en taken die in v2 zijn gemaakt, worden niet weergegeven in v3 omdat ze niet zijn gekoppeld aan een transformatie. De aanbeveling is om over te schakelen naar v3 Transforms en Jobs. Er zal een relatief korte periode van noodzaak om de inflight v2 Jobs te controleren tijdens de omschakeling.
    * Kanalen en programma's die zijn gemaakt met v2 (die zijn toegewezen aan Live Events en Live Outputs in v3) kunnen niet verder worden beheerd met v3. De aanbeveling is om over te schakelen naar v3 Live Events en Live Outputs bij een handige Kanaalstop.<br/>Op dit moment u niet continu worden uitgevoerd kanalen.  

> [!NOTE]
> Deze pagina zal worden onderhouden als de Media Services team maakt voortdurende verbeteringen aan de v3 API's en pakt de hiaten tussen de versies.

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: Een extern bestand coderen op basis van URL en de video streamen - .NET](stream-files-dotnet-quickstart.md)
