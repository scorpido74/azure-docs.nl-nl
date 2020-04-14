---
title: Azure CDN optimaliseren voor het type inhoudsweergave
description: Azure CDN optimaliseren voor het type inhoudsweergave
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 473636dc95d96ea348a42ec0f1090029bf3a7728
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260433"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Azure CDN optimaliseren voor het type inhoudsweergave

Wanneer u inhoud levert aan een groot wereldwijd publiek, is het van cruciaal belang om de geoptimaliseerde levering van uw inhoud te garanderen. [Azure Content Delivery Network (CDN)](cdn-overview.md) kan de leveringservaring optimaliseren op basis van het type inhoud dat u hebt. De inhoud kan een website, een live stream, een video of een groot bestand zijn om te downloaden. Wanneer u een CDN-eindpunt maakt, geeft u een scenario op in de **optie Geoptimaliseerd voor.** Uw keuze bepaalt welke optimalisatie wordt toegepast op de inhoud die wordt geleverd vanaf het CDN-eindpunt.

Optimalisatiekeuzes zijn ontworpen om best-practice gedrag te gebruiken om de prestaties van de inhoudsweergave te verbeteren en de oorsprong te verbeteren. Uw scenariokeuzes zijn van invloed op de prestaties door configuraties voor gedeeltelijke caching, objectchunking en het beleid voor het opnieuw proberen van oorsprongsfout te wijzigen. 

Dit artikel geeft een overzicht van verschillende optimalisatiefuncties en wanneer u ze moet gebruiken. Zie de desbetreffende artikelen over elk afzonderlijk optimalisatietype voor meer informatie over functies en beperkingen.

> [!NOTE]
> Wanneer u een CDN-eindpunt maakt, kan het **geoptimaliseerd voor** opties variëren op basis van het type profiel waarin het eindpunt wordt gemaakt. Azure CDN-providers passen verbetering op verschillende manieren toe, afhankelijk van het scenario. 

## <a name="provider-options"></a>Provideropties

**Azure CDN Standard van Microsoft-profielen** ondersteunt de volgende optimalisaties:

* [Algemene weblevering](#general-web-delivery). Deze optimalisatie wordt ook gebruikt voor mediastreaming en het downloaden van grote bestanden.

> [!NOTE]
> Dynamische siteversnelling van Microsoft wordt aangeboden via [Azure Front Door Service.](https://docs.microsoft.com/azure/frontdoor/front-door-overview)

**Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon-profielen** ondersteunen de volgende optimalisaties:

* [Algemene weblevering](#general-web-delivery). Deze optimalisatie wordt ook gebruikt voor mediastreaming en het downloaden van grote bestanden.

* [Dynamische siteversnelling](#dynamic-site-acceleration) 


**Azure CDN Standard van Akamai-profielen** ondersteunen de volgende optimalisaties:

* [Algemene weblevering](#general-web-delivery) 

* [Algemene mediastreaming](#general-media-streaming)

* [Video-on-demand mediastreaming](#video-on-demand-media-streaming)

* [Groot bestand downloaden](#large-file-download)

* [Dynamische siteversnelling](#dynamic-site-acceleration) 

Microsoft raadt u aan prestatievariaties tussen verschillende providers te testen om de optimale provider voor uw levering te selecteren.

## <a name="select-and-configure-optimization-types"></a>Optimalisatietypen selecteren en configureren

Wanneer u een CDN-eindpunt maakt, selecteert u een optimalisatietype dat het beste overeenkomt met het scenario en het type inhoud dat het eindpunt moet leveren. **Algemene weblevering** is de standaardselectie. Alleen voor bestaande **Azure CDN-standaard van Akamai-eindpunten** u de optimalisatieoptie op elk gewenst moment bijwerken. Deze wijziging onderbreekt de levering van Azure CDN niet. 

1. Selecteer in een **Azure CDN-standaard vanuit akamai-profiel** een eindpunt.

    ![Eindpuntselectie](./media/cdn-optimization-overview/01_Akamai.png)

2. Selecteer onder INSTELLINGEN de optie **Optimalisatie**. Selecteer vervolgens een type in de vervolgkeuzelijst **Geoptimaliseerd voor.**

    ![Optimalisatie en typeselectie](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimalisatie voor specifieke scenario's

U het CDN-eindpunt optimaliseren voor een van deze scenario's. 

### <a name="general-web-delivery"></a>Algemene weblevering

Algemene weblevering is de meest voorkomende optimalisatieoptie. Het is ontworpen voor algemene webcontentoptimalisatie, zoals webpagina's en webtoepassingen. Deze optimalisatie kan ook worden gebruikt voor het downloaden van bestanden en video's.

Een typische website bevat statische en dynamische inhoud. Statische inhoud omvat afbeeldingen, JavaScript-bibliotheken en stijlbladen die in de cache kunnen worden opgeslagen en aan verschillende gebruikers kunnen worden geleverd. Dynamische inhoud is gepersonaliseerd voor een individuele gebruiker, zoals nieuwsberichten die zijn afgestemd op een gebruikersprofiel. Dynamische inhoud, zoals de inhoud van winkelwagentjes, wordt niet in de cache opgeslagen omdat deze uniek is voor elke gebruiker. Algemene weblevering kan uw volledige website optimaliseren. 

> [!NOTE]
> Als u een **Azure CDN-standaard uit akamai-profiel** gebruikt, selecteert u dit optimalisatietype als de gemiddelde bestandsgrootte kleiner is dan 10 MB. Als uw gemiddelde bestandsgrootte anders groter is dan 10 MB, selecteert u **Groot bestand downloaden** van de vervolgkeuzelijst Geoptimaliseerd **voor.**

### <a name="general-media-streaming"></a>Algemene mediastreaming

Als u het eindpunt moet gebruiken voor live streaming en video-on-demand streaming, selecteert u het algemene optimalisatietype voor mediastreaming.

Mediastreaming is tijdsgevoelig, omdat pakketten die te laat op de client aankomen, zoals frequente buffering van video-inhoud, een verminderde kijkervaring kunnen veroorzaken. Media streaming optimalisatie vermindert de latentie van de levering van media-inhoud en biedt een soepele streaming ervaring voor gebruikers. 

Dit scenario is gebruikelijk voor Azure-mediaserviceklanten. Wanneer u Azure mediaservices gebruikt, krijgt u één streamingeindpunt dat kan worden gebruikt voor zowel live als on-demand streaming. Met dit scenario hoeven klanten niet over te schakelen naar een ander eindpunt wanneer ze overstappen van live naar on-demand streaming. Algemene media streaming optimalisatie ondersteunt dit soort scenario.

Voor **Azure CDN Standard van Microsoft**, Azure **CDN Standard van Verizon**en Azure **CDN Premium van Verizon**, gebruikt u het algemene type optimalisatie van webweergave om algemene streamingmedia-inhoud te leveren.

Zie [Optimalisatie van mediastreaming](cdn-media-streaming-optimization.md)voor meer informatie over optimalisatie voor mediastreaming.

### <a name="video-on-demand-media-streaming"></a>Video-on-demand mediastreaming

Video-on-demand mediastreaming-optimalisatie verbetert video-on-demand streaming content. Als u een eindpunt gebruikt voor video-on-demand streaming, gebruikt u deze optie.

Voor **Azure CDN Standard van Microsoft,** **Azure CDN Standard van Verizon**en Azure **CDN Premium van Verizon-profielen** gebruikt u het algemene optimalisatietype voor weblevering om video-on-demand streaming media-inhoud te leveren.

Zie [Optimalisatie van mediastreaming](cdn-media-streaming-optimization.md)voor meer informatie over optimalisatie voor mediastreaming.

> [!NOTE]
> Als het CDN-eindpunt voornamelijk video-on-demand-inhoud bedient, gebruikt u dit optimalisatietype. Het grote verschil tussen dit optimalisatietype en het algemene optimalisatietype voor mediastreaming is de time-out van de verbinding opnieuw proberen. De time-out is veel korter om te werken met live streaming scenario's.
>

### <a name="large-file-download"></a>Groot bestand downloaden

Voor **Azure CDN Standard van Akamai-profielen** zijn grote bestandsdownloads geoptimaliseerd voor inhoud groter dan 10 MB. Als uw gemiddelde bestandsgrootte kleiner is dan 10 MB, gebruikt u algemene webbezorging. Als uw gemiddelde bestandsgrootte consistent groter is dan 10 MB, is het mogelijk efficiënter om een apart eindpunt voor grote bestanden te maken. Firmware of software-updates zijn bijvoorbeeld meestal grote bestanden. Om bestanden groter dan 1,8 GB te leveren, is de grote optimalisatie van het downloaden van bestanden vereist.

Voor **Azure CDN Standard van Microsoft,** **Azure CDN Standard van Verizon**en Azure **CDN Premium van Verizon-profielen** gebruikt u het algemene optimalisatietype voor weblevering om grote inhoud voor het downloaden van bestanden te leveren. Er is geen beperking op de grootte van het downloaden van bestanden.

Zie [Grote bestandsoptimalisatie](cdn-large-file-optimization.md)voor meer informatie over grote bestandsoptimalisatie.

### <a name="dynamic-site-acceleration"></a>Dynamische siteversnelling

 Dynamic site acceleration (DSA) is beschikbaar voor **Azure CDN Standard van Akamai,** **Azure CDN Standard van Verizon**en Azure **CDN Premium van** Verizon-profielen. Deze optimalisatie houdt een extra vergoeding in om te gebruiken; Zie [Content Delivery Network-prijzen voor](https://azure.microsoft.com/pricing/details/cdn/)meer informatie.

> [!NOTE]
> Dynamische siteversnelling van Microsoft wordt aangeboden via [Azure Front Door Service,](https://docs.microsoft.com/azure/frontdoor/front-door-overview) een wereldwijde [anycast-service](https://en.wikipedia.org/wiki/Anycast) die gebruikmaakt van het privéwereldwijde netwerk van Microsoft om uw app-workloads te leveren.

DSA bevat verschillende technieken die de latentie en prestaties van dynamische inhoud ten goede komen. Technieken omvatten route- en netwerkoptimalisatie, TCP-optimalisatie en meer. 

U deze optimalisatie gebruiken om een web-app te versnellen die tal van reacties bevat die niet in de cache kunnen worden opgeslagen. Voorbeelden zijn zoekresultaten, afrekentransacties of realtime gegevens. U de belangrijkste Azure CDN-caching-mogelijkheden blijven gebruiken voor statische gegevens. 

Zie [Dynamische siteversnelling](cdn-dynamic-site-acceleration.md)voor meer informatie over dynamische siteversnelling.



