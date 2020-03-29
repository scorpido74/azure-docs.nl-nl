---
title: Overzicht van het eindpunt van Azure Media Services Streaming | Microsoft Documenten
description: In dit artikel vindt u een overzicht van streamingeindpunten van Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 95d8d819aa1b418b4a7ec736cef64cb989f7e37b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74885633"
---
# <a name="streaming-endpoints-overview"></a>Overzicht van streaming eindpunten  

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

In Microsoft Azure Media Services (AMS) vertegenwoordigt een **streamingeindpunt** een streamingservice die inhoud rechtstreeks kan leveren aan een clientplayertoepassing of aan een CONTENT Delivery Network (CDN) voor verdere distributie. Media Services biedt ook naadloze Azure CDN-integratie. De uitgaande stream van een StreamingEndpoint-service kan een live stream zijn, een video op aanvraag of het progressief downloaden van uw asset in uw Media Services-account. Elk Azure Media Services-account bevat een standaard StreamingEndpoint. Onder het account kunnen extra StreamingEndpoints worden aangemaakt. Er zijn twee versies van StreamingEndpoints, 1.0 en 2.0. Vanaf 10 januari 2017 bevatten nieuw aangemaakte AMS-accounts **default** standaard StreamingEndpoint van versie 2.0. Extra streaming eindpunten die je toevoegt aan dit account zijn ook versie 2.0. Deze wijziging heeft geen invloed op de bestaande accounts; bestaande StreamingEndpoints zijn versie 1.0 en kunnen worden geüpgraded naar versie 2.0. Met deze wijziging zijn er gedrags-, facturerings- en functiewijzigingen (zie voor meer informatie de sectie **Streamingtypen en -versies** hieronder).

Azure Media Services heeft de volgende eigenschappen toegevoegd aan de entiteit Streaming Endpoint: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Voor een gedetailleerd overzicht van deze eigenschappen, zie [dit](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Wanneer u een Azure Media Services-account maakt, wordt een standaard streamingeindpunt voor u gemaakt in de **status Gestopt.** U het standaard eindpunt voor streaming niet verwijderen. Afhankelijk van de Azure CDN-beschikbaarheid in het doelgebied, bevat standaard standaard streaming eindpunt standaardstreaming ook de integratie van cdn-provider 'StandardVerizon'. 
                
> [!NOTE]
> Azure CDN-integratie kan worden uitgeschakeld voordat het streaming-eindpunt wordt gestart. De `hostname` URL en de streaming blijft hetzelfde, ongeacht of u CDN inschakelt.

Dit onderwerp geeft een overzicht van de belangrijkste functionaliteiten die worden geleverd door streaming endpoints.

## <a name="naming-conventions"></a>Naamconventies

Voor het standaardeindpunt:`{AccountName}.streaming.mediaservices.windows.net`

Voor eventuele extra eindpunten:`{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Streamingtypen en -versies

### <a name="standardpremium-types-version-20"></a>Standaard/Premium-typen (versie 2.0)

Vanaf de release van Media Services in januari 2017 heb je twee streamingtypen: **Standard** (preview) en **Premium**. Deze typen maken deel uit van de streaming endpoint-versie "2.0".


|Type|Beschrijving|
|--------|--------|  
|**Standaard**|Het standaard streamingeindpunt is een **standaardtype,** kan worden gewijzigd in het Premium-type door streaming-eenheden aan te passen.|
|**Premium** |Deze optie is geschikt voor professionele scenario's die een hogere schaal of controle vereisen. U gaat naar een **Premium-type** door streaming-eenheden aan te passen.<br/>Speciale streaming eindpunten leven in geïsoleerde omgeving en concurreren niet om resources.|

Voor klanten die content willen leveren aan grote internetdoelgroepen, raden we u aan CDN in te schakelen op het Streaming Endpoint.

Zie de volgende sectie [Streamingtypen vergelijken](#comparing-streaming-types) voor meer gedetailleerde informatie.

### <a name="classic-type-version-10"></a>Klassiek type (versie 1.0)

Voor gebruikers die AMS-accounts hebben gemaakt vóór de release van 10 januari 2017, hebt u een **Klassiek-type** streaming-eindpunt. Dit type maakt deel uit van de streaming endpoint versie "1.0".

Als uw **versie "1.0"** streaming endpoint >=1 premium streaming units (SU) heeft, is dit premium streaming endpoint en biedt het alle AMS-functies (net als het **standaard/Premium-type)** zonder extra configuratiestappen.

>[!NOTE]
>**Klassieke** streaming eindpunten (versie "1.0" en 0 SU), biedt beperkte functies en bevat geen SLA. Het wordt aanbevolen om te migreren naar **standaardtype** om een betere ervaring te krijgen en functies zoals dynamische verpakking of versleuteling en andere functies die bij het **standaardtype** worden geleverd, te gebruiken. Als u wilt migreren naar het **standaardtype,** gaat u naar de [Azure-portal](https://portal.azure.com/) en selecteert **u Opt-in naar Standard.** Zie de [migratiesectie](#migration-between-types) voor meer informatie over migratie.
>
>Let op dat deze bewerking niet kan worden teruggedraaid en heeft een prijseffect.
>
 
## <a name="comparing-streaming-types"></a>Streamingtypen vergelijken

### <a name="versions"></a>Versies

|Type|StreamingEndpointVersie|Schaaleenheden|CDN|Billing|
|--------------|----------|-----------------|-----------------|-----------------|
|Klassiek|1.0|0|N.v.t.|Gratis|
|Standaardeindpunt voor streaming (voorbeeld)|2.0|0|Ja|Betaald|
|Premium streaming-eenheden|1.0|>0|Ja|Betaald|
|Premium streaming-eenheden|2.0|>0|Ja|Betaald|

### <a name="features"></a>Functies

Functie|Standard|Premium
---|---|---
Doorvoer |Tot 600 Mbps en kan een veel hogere effectieve doorvoer bieden wanneer een CDN wordt gebruikt.|200 Mbps per streaming unit (SU). Kan zorgen voor een veel hogere effectieve doorvoer wanneer een CDN wordt gebruikt.
CDN|Azure CDN, CDN van derden of geen CDN.|Azure CDN, CDN van derden of geen CDN.
Facturering wordt naar rato berekend| Dagelijks|Dagelijks
Dynamische versleuteling|Ja|Ja
Dynamische verpakking|Ja|Ja
Schalen|Auto schaalt op naar de beoogde doorvoer.|Extra streaming-eenheden.
IP-filtering/G20/Aangepaste host <sup>1</sup>|Ja|Ja
Progressieve download|Ja|Ja
Aanbevolen gebruik |Aanbevolen voor de overgrote meerderheid van de streaming scenario's.|Professioneel gebruik. 

<sup>1</sup> Alleen direct gebruikt op het streaming eindpunt wanneer het CDN niet is ingeschakeld op het eindpunt.<br/>

Zie [Prijzen en SLA](https://azure.microsoft.com/pricing/details/media-services/)voor SLA-informatie .

## <a name="migration-between-types"></a>Migratie tussen typen

Van | Handeling | Actie
---|---|---
Klassiek|Standard|Noodzaak om je aan te melden
Klassiek|Premium| Schalen(extra streaming-eenheden)
Standaard/Premium|Klassiek|Niet beschikbaar(Als streaming endpoint-versie 1.0 is. Het is toegestaan om over te schakelen naar klassiek met het instellen van schaaleenheden naar "0")
Standaard (met/zonder CDN)|Premium met dezelfde configuraties|Toegestaan in de **gestarte** staat. (via Azure portal)
Premium (met/zonder CDN)|Standaard met dezelfde configuraties|Toegestaan in de **gestarte** status (via Azure-portal)
Standaard (met/zonder CDN)|Premium met verschillende config|Toegestaan in de **gestopte** status (via Azure-portal). Niet toegestaan in de loopstaat.
Premium (met/zonder CDN)|Standaard met verschillende config|Toegestaan in de **gestopte** status (via Azure-portal). Niet toegestaan in de loopstaat.
Versie 1.0 met SU >= 1 met CDN|Standaard/Premium zonder CDN|Toegestaan in de **gestopte** staat. Niet toegestaan in de **gestarte** staat.
Versie 1.0 met SU >= 1 met CDN|Standaard met/zonder CDN|Toegestaan in de **gestopte** staat. Niet toegestaan in de **gestarte** staat. Versie 1.0 CDN zal worden verwijderd en nieuwe gemaakt en gestart.
Versie 1.0 met SU >= 1 met CDN|Premium met/zonder CDN|Toegestaan in de **gestopte** staat. Niet toegestaan in de **gestarte** staat. Klassieke CDN zal worden verwijderd en nieuwe gemaakt en gestart.

## <a name="next-steps"></a>Volgende stappen
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

