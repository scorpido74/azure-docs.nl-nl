---
title: Azure CDN realtime waarschuwingen | Microsoft Documenten
description: Realtime waarschuwingen in Microsoft Azure CDN. Realtime waarschuwingen geven meldingen over de prestaties van de eindpunten in uw CDN-profiel.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 4b8cbc27757cf6c321ea4b3c27720a129aa27c1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593481"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Realtime waarschuwingen in Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Overzicht
In dit document worden realtime waarschuwingen in Microsoft Azure CDN uitgelegd. Deze functionaliteit biedt realtime meldingen over de prestaties van de eindpunten in uw CDN-profiel.  U e-mail- of HTTP-waarschuwingen instellen op basis van:

* Bandbreedte
* Statuscodes
* Cachestatussen
* Verbindingen

## <a name="creating-a-real-time-alert"></a>Een realtime waarschuwing maken
1. Blader in de [Azure-portal](https://portal.azure.com)naar uw CDN-profiel.
   
    ![CDN-profiel](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. Klik in het CDN-profielblad op de knop **Beheren.**
   
    ![Knop CDN-profielbeheer](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Het CDN-beheerportaal wordt geopend.
3. Plaats de plaats op het tabblad **Analytics** en plaats de plaats vervolgens boven de **flyout voor realtime statistieken.**  Klik op **realtime waarschuwingen**.
   
    ![CDN-beheerportal](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    De lijst met bestaande waarschuwingsconfiguraties (indien aanwezig) wordt weergegeven.
4. Klik op de knop **Waarschuwing toevoegen.**
   
    ![Knop Waarschuwing toevoegen](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Er wordt een formulier weergegeven voor het maken van een nieuwe waarschuwing.
   
    ![Nieuw waarschuwingsformulier](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Als u wilt dat deze waarschuwing actief is wanneer u op **Opslaan**klikt, schakelt u het selectievakje **Waarschuwing ingeschakeld** in.
6. Voer een beschrijvende naam in voor uw waarschuwing in het veld **Naam.**
7. Selecteer in de vervolgkeuzelijst **Mediatype** de optie **HTTP Groot object**.
   
    ![Mediatype met HTTP-groot object geselecteerd](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > U moet **HTTP Large Object** selecteren als **mediatype**.  De andere keuzes worden niet gebruikt door **Azure CDN van Verizon**.  Als u HTTP Large Object niet **selecteert,** wordt uw waarschuwing nooit geactiveerd.
   > 
   > 
8. Maak een **expressie** die u wilt bewaken door een **waarde met riek,** **operator**en **trigger te**selecteren .
   
   * Selecteer **bij Metric**het type voorwaarde dat u wilt controleren.  **Bandbreedte Mbps** is de hoeveelheid bandbreedte gebruik in megabits per seconde.  **Total Connections** is het aantal gelijktijdige HTTP-verbindingen met onze edge-servers.  Zie [Azure CDN-cachestatuscodes](/previous-versions/azure/mt759237(v=azure.100)) en [Azure CDN HTTP-statuscodes](/previous-versions/azure/mt759238(v=azure.100)) voor definities van de verschillende cachestatussen en statuscodes van De Cache
   * **Operator** is de wiskundige operator die de relatie tussen de metrische en de triggerwaarde vaststelt.
   * **Triggerwaarde** is de drempelwaarde waaraan moet worden voldaan voordat een melding wordt verzonden.
     
     In het volgende voorbeeld geeft de gemaakte expressie aan dat een melding wordt verzonden wanneer het aantal 404-statuscodes groter is dan 25.
     
     ![Voorbeeldexpressie voor realtime waarschuwingen](./media/cdn-real-time-alerts/cdn-expression.png)
9. Voer bij **Interval**in hoe vaak u de expressie wilt evalueren.
10. Selecteer **in de vervolgkeuzelijst Melden** op wanneer u een melding wilt ontvangen wanneer de expressie waar is.
    
    * **Voorwaarde Start** geeft aan dat er een melding wordt verzonden wanneer de opgegeven voorwaarde voor het eerst wordt gedetecteerd.
    * **Voorwaarde Einde** geeft aan dat er een melding wordt verzonden wanneer de opgegeven voorwaarde niet meer wordt gedetecteerd. Deze melding kan alleen worden geactiveerd nadat ons netwerkbewakingssysteem heeft gedetecteerd dat de opgegeven voorwaarde is opgetreden.
    * **Continu** geeft aan dat er elke keer een melding wordt verzonden dat het netwerkbewakingssysteem de opgegeven toestand detecteert. Houd er rekening mee dat het netwerkbewakingssysteem slechts één keer per interval controleert op de opgegeven voorwaarde.
    * **Voorwaarde Start en Einde** geeft aan dat een melding wordt verzonden de eerste keer dat de opgegeven voorwaarde wordt gedetecteerd en nogmaals wanneer de voorwaarde niet meer wordt gedetecteerd.
1. Als u meldingen per e-mail wilt ontvangen, schakelt u het selectievakje **Melden per e-mail** in.  
    
    ![Melding per e-mailformulier](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    Voer in het veld **Aan** het e-mailadres in waar u meldingen wilt verzenden. Voor **Onderwerp** en **hoofdtekst**u de standaardinstelling verlaten of u het bericht aanpassen met de lijst **Beschikbare zoekwoorden** om waarschuwingsgegevens dynamisch in te voegen wanneer het bericht wordt verzonden.
    
    > [!NOTE]
    > U de e-mailmelding testen door op de knop **Melding testen** te klikken, maar alleen nadat de waarschuwingsconfiguratie is opgeslagen.
    > 
    > 
12. Als u wilt dat meldingen op een webserver worden geplaatst, schakelt u het selectievakje **Melden per HTTP-bericht in.**
    
    ![Melden via http-postformulier](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    Voer **in** het veld Url de URL in waar u het HTTP-bericht wilt plaatsen. Voer in het tekstvak **Kopteksten** de HTTP-headers in die in de aanvraag moeten worden verzonden.  Voor **Body**u het bericht aanpassen met behulp van de lijst **Beschikbare zoekwoorden** om waarschuwingsgegevens dynamisch in te voegen wanneer het bericht wordt verzonden.  **Kop-** en **hoofdtekst** standaard voor een XML-payload die vergelijkbaar is met het volgende voorbeeld:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > U de http-berichtmelding testen door op de knop **Melding testen** te klikken, maar alleen nadat de waarschuwingsconfiguratie is opgeslagen.
    > 
    > 
13. Klik **op** de knop Opslaan om de waarschuwingsconfiguratie op te slaan.  Als u **Alert ingeschakeld** in stap 5 hebt aangevinkt, is uw waarschuwing nu actief.

## <a name="next-steps"></a>Volgende stappen
* [Realtime statistieken analyseren in Azure CDN](cdn-real-time-stats.md)
* Graaf dieper met [geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md)
* [Gebruikspatronen analyseren](cdn-analyze-usage-patterns.md)

