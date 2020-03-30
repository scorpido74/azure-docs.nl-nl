---
title: Azure CDN-cachinggedrag beheren met querytekenreeksen - standaardlaag
description: Azure CDN-querytekenreeksen bepalen hoe bestanden in de cache worden opgeslagen wanneer een webaanvraag een querytekenreeks bevat. In dit artikel worden querytekenreeksen beschreven in Azure CDN-standaardproducten.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 6471241527dd9b594eaaca20ebc75cacb27f8f72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083033"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Azure CDN-cachinggedrag beheren met querytekenreeksen - standaardlaag
> [!div class="op_single_selector"]
> * [Standaardlaag](cdn-query-string.md)
> * [Premiumlaag](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Overzicht
Met Azure Content Delivery Network (CDN) u bepalen hoe bestanden in de cache worden opgeslagen voor een webaanvraag die een querytekenreeks bevat. In een webaanvraag met een querytekenreeks is de querytekenreeks dat gedeelte van de aanvraag dat optreedt na een vraagteken (?). Een querytekenreeks kan een of meer sleutelwaardeparen bevatten, waarbij de veldnaam en de waarde ervan worden gescheiden door een gelijk teken (=). Elk sleutelwaardepaar wordt gescheiden door een ampère (&). Http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Als er meer dan één sleutelwaardepaar in een queryreeks van een aanvraag zit, maakt de volgorde er niet toe. 

> [!IMPORTANT]
> De Azure CDN-standaard- en premiumproducten bieden dezelfde functionaliteit voor het plaatsen van querytekenreeksen, maar de gebruikersinterface is anders. In dit artikel wordt de interface voor **Azure CDN Standard van Microsoft,** **Azure CDN Standard van Akamai** en **Azure CDN Standard van Verizon beschreven.** Zie Azure [CDN-cachinggedrag met querytekenreeksen beheren met querytekenreeksen - premiumlaag](cdn-query-string-premium.md). **Azure CDN Premium from Verizon**

Er zijn drie querytekenreeksmodi beschikbaar:

- **Querytekenreeksen negeren:** standaardmodus. In deze modus geeft het POP-knooppunt (POINT-point-of-presence) van CDN de querytekenreeksen van de aanvrager door naar de oorsprongsserver op het eerste verzoek en caches het element. Alle volgende aanvragen voor het item die worden weergegeven vanuit de POP negeren de querytekenreeksen totdat het in de cache opgeslagen item is verlopen.

- **Caching voor querytekenreeksen omzeilen:** in deze modus worden aanvragen met querytekenreeksen niet opgeslagen in het CDN POP-knooppunt. Het POP-knooppunt haalt het item rechtstreeks van de oorsprongsserver op en geeft het bij elke aanvraag door aan de aanvrager.

- **Cache elke unieke URL:** In deze modus wordt elk verzoek met een unieke URL, inclusief de querytekenreeks, behandeld als een uniek item met een eigen cache. Het antwoord van de oorsprongsserver voor een aanvraag voor bijvoorbeeld ashx?q=test1 wordt bijvoorbeeld in de cache opgeslagen op het POP-knooppunt en geretourneerd voor volgende caches met dezelfde querytekenreeks. Een aanvraag voor bijvoorbeeld.ashx?q=test2 wordt in de cache opgeslagen als een afzonderlijk item met een eigen time-to-live-instelling.
   
    >[!IMPORTANT] 
    > Gebruik deze modus niet wanneer de querytekenreeks parameters bevat die bij elk verzoek worden gewijzigd, zoals een sessie-id of een gebruikersnaam, omdat dit resulteert in een lage cache-hitratio.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Instellingen voor het plaatsen van querytekenreeksen wijzigen voor standaard CDN-profielen
1. Open een CDN-profiel en selecteer vervolgens het CDN-eindpunt dat u wilt beheren.
   
   ![CDN-profieleindpunten](./media/cdn-query-string/cdn-endpoints.png)
   
2. Klik in het linkerdeelvenster onder Instellingen op **Regels voor caching**.
   
    ![Knop Regels voor CDN-caching](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Selecteer in de lijst **Querytekenreeks caching-gedrag** een querytekenreeksmodus en klik op **Opslaan**.
   
   ![Opties voor caching van CDN-querytekenreeksen](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Omdat het tijd kost voordat de registratie wordt doorgegeven via Azure CDN, zijn wijzigingen in cachetekenreeksen mogelijk niet direct zichtbaar:
> - Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
> - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
> - Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden normaal gesproken binnen 10 minuten doorgegeven. 



