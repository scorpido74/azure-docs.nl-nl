---
title: Azure CDN-cachinggedrag beheren met querytekenreeksen - premiumlaag
description: Azure CDN-querytekenreeksen bepalen hoe bestanden in de cache worden opgeslagen wanneer een webaanvraag een querytekenreeks bevat. In dit artikel wordt querytekenreeksen in het Azure CDN Premium van verizon-product beschreven.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 94949a31db5321929a3440281cebd01712c79bb8
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260136"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Azure CDN-cachinggedrag beheren met querytekenreeksen - premiumlaag
> [!div class="op_single_selector"]
> * [Standaardlaag](cdn-query-string.md)
> * [Premiumlaag](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Overzicht
Met Azure Content Delivery Network (CDN) u bepalen hoe bestanden in de cache worden opgeslagen voor een webaanvraag die een querytekenreeks bevat. In een webaanvraag met een querytekenreeks is de querytekenreeks dat gedeelte van de aanvraag dat optreedt na een vraagteken (?). Een querytekenreeks kan een of meer sleutelwaardeparen bevatten, waarbij de veldnaam en de waarde ervan worden gescheiden door een gelijk teken (=). Elk sleutelwaardepaar wordt gescheiden door een ampère (&). Http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Als er meer dan één sleutelwaardepaar in een queryreeks van een aanvraag zit, maakt de volgorde er niet toe. 

> [!IMPORTANT]
> De standaard- en premium CDN-producten bieden dezelfde functionaliteit voor het caching van querytekenreeksen, maar de gebruikersinterface is anders. In dit artikel wordt de interface voor **Azure CDN Premium van Verizon beschreven.** Zie Azure [CDN-cachinggedrag met querytekenreeksen beheren met querytekenreeksen - standaardlaag](cdn-query-string.md).
>


Er zijn drie querytekenreeksmodi beschikbaar:

- **standaardcache:** standaardmodus. In deze modus geeft het POP-knooppunt (POINT-point-of-presence) van CDN de querytekenreeksen van de aanvrager door naar de oorsprongsserver op het eerste verzoek en caches het element. Alle volgende aanvragen voor het item die vanaf de POP-server worden aangeboden, negeren de querytekenreeksen totdat het in de cache opgeslagen element is verlopen.

    >[!IMPORTANT] 
    > Als tokenautorisatie is ingeschakeld voor elk pad op dit account, is de standaardcachemodus de enige modus die kan worden gebruikt. 

- **no-cache:** In deze modus worden aanvragen met querytekenreeksen niet opgeslagen in het CDN POP-knooppunt. Het POP-knooppunt haalt het item rechtstreeks van de oorsprongsserver op en geeft het bij elke aanvraag door aan de aanvrager.

- **unieke cache:** In deze modus wordt elk verzoek met een unieke URL, inclusief de querytekenreeks, behandeld als een uniek item met een eigen cache. Het antwoord van de oorsprongsserver voor een aanvraag voor bijvoorbeeld ashx?q=test1 wordt bijvoorbeeld in de cache opgeslagen op het POP-knooppunt en geretourneerd voor volgende caches met dezelfde querytekenreeks. Een aanvraag voor bijvoorbeeld.ashx?q=test2 wordt in de cache opgeslagen als een afzonderlijk item met een eigen time-to-live-instelling.
   
    >[!IMPORTANT] 
    > Gebruik deze modus niet wanneer de querytekenreeks parameters bevat die bij elk verzoek worden gewijzigd, zoals een sessie-id of een gebruikersnaam, omdat dit resulteert in een lage cache-hitratio.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Instellingen voor het plaatsen van querytekenreeksen wijzigen voor premium CDN-profielen
1. Open een CDN-profiel en klik op **Beheren**.
   
    ![Knop CDN-profiel beheren](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Het CDN-beheerportaal wordt geopend.
2. Plaats de plaats op het tabblad **HTTP Large** en plaats de plaats vervolgens boven het flyout-menu **Cache-instellingen.** Klik **op Caching van querytekenreeksen**.
   
    Opties voor het plaatsen van querytekenreeksen worden weergegeven.
   
    ![Opties voor caching van CDN-querytekenreeksen](./media/cdn-query-string-premium/cdn-query-string.png)
3. Selecteer een querytekenreeksmodus en klik op **Bijwerken**.

> [!IMPORTANT]
> Omdat het tijd kost voordat de registratie zich door het CDN verspreidt, zijn wijzigingen in de cachetekenreeksniet direct zichtbaar. Propagatie is meestal voltooid in 10 minuten.
 

