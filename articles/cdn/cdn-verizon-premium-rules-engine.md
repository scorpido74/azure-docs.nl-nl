---
title: HTTP-gedrag overschrijven met Azure CDN - Verizon Premium-regelsengine
description: Met de regelsengine u aanpassen hoe HTTP-aanvragen door Azure CDN van Verizon Premium worden afgehandeld, zoals het blokkeren van de levering van bepaalde typen inhoud, het definiëren van een cachebeleid en het wijzigen van HTTP-headers.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 2e5e4265f30631f8e68d8f9e7156ea578ae85e7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253472"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>HTTP-gedrag overschrijven met de Azure CDN van de Verizon Premium-regelsengine

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Overzicht

Met de Azure CDN-regels-engine u aanpassen hoe HTTP-aanvragen worden behandeld. Bijvoorbeeld het blokkeren van de levering van bepaalde inhoudstypen, het definiëren van een cachingbeleid of het wijzigen van een HTTP-header. In deze zelfstudie wordt uitgelegd hoe u een regel maakt die het cachinggedrag van CDN-elementen wijzigt. Zie Naslaginformatie over de engine syntaxis van [Azure CDN-regels](cdn-verizon-premium-rules-engine-reference.md)voor meer informatie over de syntaxis van de engine voor regels .

## <a name="access"></a>Toegang

Als u toegang wilt krijgen tot de rules engine, moet u eerst **Beheren** boven aan de **CDN-profielpagina** selecteren om toegang te krijgen tot de Azure CDN-beheerpagina. Afhankelijk van of uw eindpunt is geoptimaliseerd voor dynamische siteversnelling (DSA), krijgt u vervolgens toegang tot de regelsengine met de set regels die geschikt zijn voor uw type eindpunt:

- Eindpunten geoptimaliseerd voor algemene weblevering of andere niet-DSA-optimalisatie:
    
    Selecteer het tabblad **HTTP Groot** en selecteer Vervolgens **Rules Engine**.

    ![Regels engine voor HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Eindpunten geoptimaliseerd voor DSA:
    
    Selecteer het tabblad **ADN** en selecteer **Vervolgens Rules Engine**.
    
    ADN is een term die door Verizon wordt gebruikt om DSA-inhoud op te geven. Alle regels die u hier maakt, worden genegeerd door eindpunten in uw profiel die niet zijn geoptimaliseerd voor DSA.

    ![Regels motor voor DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Zelfstudie

1. Selecteer beheren op de profielpagina **van** **CDN** .
   
    ![Knop CDN-profiel beheren](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Het CDN-beheerportaal wordt geopend.

2. Selecteer het tabblad **HTTP Groot** en selecteer Vervolgens **Rules Engine**.
   
    De opties voor een nieuwe regel worden weergegeven.
   
    ![CDN nieuwe regelopties](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > De volgorde waarin meerdere regels worden weergegeven, is van invloed op de manier waarop ze worden behandeld. Een volgende regel kan de acties overschrijven die door een vorige regel zijn opgegeven.
   >

3. Voer een naam in het tekstvak **Naam / Beschrijving** in.

4. Identificeer het type aanvragen waar de regel op van toepassing is. Gebruik de standaardvoorwaarde voor **overeenkomen, Altijd**.
   
   ![CDN-regelovereenkomst voorwaarde](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Er zijn meerdere wedstrijdvoorwaarden beschikbaar in de vervolgkeuzelijst. Selecteer het blauwe informatiepictogram links voor informatie over de momenteel geselecteerde wedstrijdvoorwaarde.
   >
   >  Zie [Voorwaardelijke expressies van regels engine voor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)een gedetailleerde lijst met voorwaardelijke expressies.
   >  
   > Zie [Regels motor match voorwaarden](cdn-verizon-premium-rules-engine-reference-match-conditions.md)voor een gedetailleerde lijst met wedstrijdvoorwaarden.
   >
   >

5. Als u een nieuwe **+** functie wilt toevoegen, selecteert u de knop naast **Functies**.  Selecteer in de vervolgkeuzelijst aan de linkerkant **Force Internal Max-Age**.  Voer **300**in het tekstvak dat wordt weergegeven . Wijzig de resterende standaardwaarden niet.
   
   ![CDN-regelfunctie](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Er zijn meerdere functies beschikbaar in de vervolgkeuzelijst. Selecteer het blauwe informatiepictogram links voor informatie over de momenteel geselecteerde functie.
   >
   > Voor **Force Internal Max-Age**worden `Cache-Control` `Expires` de asset's en headers overschreven om te bepalen wanneer het CDN-randknooppunt het item van de oorsprong vernieuwt. In dit voorbeeld slaat het CDN-randknooppunt het element gedurende 300 seconden of 5 minuten in de cache voordat het element wordt vernieuwd van de oorsprong.
   >
   > Zie [De enginefuncties regels voor](cdn-verizon-premium-rules-engine-reference-features.md)een gedetailleerde lijst met functies .
   >
   >

6. Selecteer **Toevoegen** om de nieuwe regel op te slaan.  De nieuwe regel wacht nu op goedkeuring. Nadat de status is goedgekeurd, verandert de status van **XML in behandeling** in Actieve **XML**.
   
   > [!IMPORTANT]
   > Wijzigingen in regels kunnen tot 10 minuten duren voordat u zich voortplant via Azure CDN.
   >
   >

## <a name="see-also"></a>Zie ook

- [Overzicht van Azure CDN](cdn-overview.md)
- [Verwijzingen naar de motor](cdn-verizon-premium-rules-engine-reference.md)
- [Regels motor overeenkomen met voorwaarden](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regels engine voorwaardelijke expressies](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regels motorfuncties](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure Fridays: de krachtige nieuwe premiumfuncties](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) van Azure CDN (video)