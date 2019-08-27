---
title: Geautomatiseerde werkstromen maken op basis van een planning - Azure Logic Apps | Microsoft Docs
description: 'Zelfstudie: het maken van een terugkerende, geautomatiseerde werkstroom op basis van een planning met Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: ec29eef7e733155b205d4feda844883bbc4496c9
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051741"
---
# <a name="check-traffic-on-a-schedule-with-azure-logic-apps"></a>Verkeer controleren volgens een schema met Azure Logic Apps

Met Azure Logic Apps kunt u werkstromen automatiseren die worden uitgevoerd volgens een planning. Deze zelfstudie laat zien hoe u een [logische app](../logic-apps/logic-apps-overview.md) met een scheduler-trigger kunt bouwen die elke doordeweekse dag ‘s ochtends wordt uitgevoerd en die de reistijd, inclusief het verkeer, tussen twee plaatsen controleert. Als de tijd een bepaalde limiet overschrijdt, verzendt de logische app een e-mail met de reistijd en de extra tijd die u nodig hebt om uw bestemming te bereiken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een lege, logische app maken. 
> * Een trigger toevoegen die als scheduler moet dienen voor uw logische app.
> * Een actie toevoegen die de reistijd voor een route ophaalt.
> * Een actie toevoegen die een variabele maakt, de reistijd converteert van seconden naar minuten en dat resultaat opslaat in de variabele.
> * Een voorwaarde toevoegen die de reistijd vergelijkt met een opgegeven limiet.
> * Een actie toevoegen die een e-mailbericht verzendt als de reistijd de limiet overschrijdt.

Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Logische app op hoog niveau](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

Als u nog geen abonnement op Azure hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een e-mailaccount van een e-mailprovider die door Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](https://docs.microsoft.com/connectors/). Deze snelstartgids maakt gebruik van een Outlook.com-account. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

* Als u de reistijd voor een route wilt ophalen, hebt u een toegangssleutel nodig voor de API van Bing Kaarten. Volg de stappen in [Een Bing Kaarten-sleutel krijgen](https://msdn.microsoft.com/library/ff428642.aspx) om deze sleutel te krijgen. 

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Kies in het hoofdmenu van Azure **Een resource maken** > **Bedrijfsintegratie** > **Logische app**.

   ![Logische app maken](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app.png)

2. In het menu **Logische app maken** geeft u de informatie over uw logische app op zoals hier wordt weergegeven en beschreven. Als u klaar bent, kiest u **Vastmaken aan dashboard** > **Maken**.

   ![Informatie over logische app opgeven](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Instelling | Value | Beschrijving | 
   | ------- | ----- | ----------- | 
   | **Name** | LA-TravelTime | De naam voor uw logische app | 
   | **Abonnement** | <*your-Azure-subscription-name*> | De naam van uw Azure-abonnement | 
   | **Resourcegroep** | LA-TravelTime-RG | De naam van de [Azure-resourcegroep](../azure-resource-manager/resource-group-overview.md) die wordt gebruikt om verwante resources te organiseren | 
   | **Location** | US - oost 2 | De regio waar u informatie over uw logische app opslaat | 
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. | 
   |||| 

3. Nadat Azure uw app heeft geïmplementeerd, wordt de Ontwerper van logische apps geopend en ziet u een pagina met een inleidende video en sjablonen voor veelgebruikte patronen voor logische apps. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Sjabloon voor lege logische app kiezen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/choose-logic-app-template.png)

Voeg vervolgens u het terugkeerpatroon [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe, die op basis van een opgegeven planning wordt geactiveerd. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer nieuwe gegevens voldoen aan een bepaalde voorwaarde. Bekijk [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie.

## <a name="add-scheduler-trigger"></a>Scheduler-trigger toevoegen

1. Typ "terugkeerpatroon" in het zoekvak van de ontwerpfunctie. Selecteer deze trigger: **Schema: terugkeer patroon**

   ![De trigger "Planning - Terugkeerpatroon" zoeken en toevoegen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

2. Op de vorm **Terugkeerpatroon** kiest u de knop met het **beletselteken** ( **...** ) en kiest u **Naam wijzigen**. Wijzig de naam van de trigger met deze beschrijving: ```Check travel time every weekday morning```

   ![Naam trigger wijzigen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

3. Kies in de trigger voor **Geavanceerde opties weergeven**.

4. Geef de gegevens van de planning en het terugkeerpatroon voor uw trigger op zoals hier wordt weergegeven en beschreven:

   ![Gegevens van de planning en het terugkeerpatroon opgeven](./media/tutorial-build-scheduled-recurring-logic-app-workflow/schedule-recurrence-trigger-settings.png)

   | Instelling | Value | Description | 
   | ------- | ----- | ----------- | 
   | **Interval** | 1 | Het aantal intervallen dat tussen controles moet worden gewacht | 
   | **Frequentie** | Week | Tijdseenheid die voor het terugkeerpatroon wordt gebruikt | 
   | **Tijdzone** | Geen | Is alleen van toepassing als u een begintijd opgeeft. Dit is handig als u een niet-lokale tijdzone opgeeft. | 
   | **Begintijd** | Geen | Het terugkeerpatroon uitstellen tot een bepaalde datum en tijd. Zie [Taken en werkstromen plannen die regelmatig worden uitgevoerd](../connectors/connectors-native-recurrence.md) voor meer informatie. | 
   | **Deze dagen** | Maandag, dinsdag, woensdag, donderdag, vrijdag | Alleen beschikbaar wanneer **Frequentie** is ingesteld op "Wekelijks" | 
   | **Deze uren** | 7, 8, 9 | Alleen beschikbaar wanneer **Frequentie** is ingesteld op 'Wekelijks' of 'Dagelijks'. Selecteer de uren van de dag waarop dit terugkeerpatroon wordt uitgevoerd. Dit voorbeeld wordt uitgevoerd om 7, 8 en 9 uur. | 
   | **Deze minuten** | 0, 15, 30, 45 | Alleen beschikbaar wanneer **Frequentie** is ingesteld op 'Wekelijks' of 'Dagelijks'. Selecteer de minuten van de dag waarop dit terugkeerpatroon wordt uitgevoerd. Dit voorbeeld wordt iedere 15 minuten na het hele uur uitgevoerd. | 
   ||||

   Deze trigger wordt iedere doordeweekse dag om de 15 minuten uitgevoerd tussen 07:00 en 09:45 uur. 
   Het vak **Preview** toont de terugkeerplanning. 
   Zie [Taken en werkstromen plannen](../connectors/connectors-native-recurrence.md) en [Werkstroomacties en -triggers](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) voor meer informatie.

5. Als u de details van de trigger voorlopig wilt verbergen, klikt u in de titelbalk van de vorm.

   ![Shape samenvouwen om details te verbergen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

6. Sla uw logische app op. Kies **Opslaan** op de werkbalk van de ontwerper. 

Uw logische app is nu live, maar doet niets anders dan herhalen. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="get-the-travel-time-for-a-route"></a>De reistijd voor een route ophalen

Nu u een trigger hebt, voegt u een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die de reistijd tussen twee locaties ophaalt. Logic Apps biedt een connector voor de API van Bing Kaarten. Hiermee kunt u deze informatie eenvoudig ophalen. Voordat u deze taak start, moet u ervoor zorgen dat u een API-sleutel van Bing Kaarten hebt, zoals beschreven in de vereisten in deze zelfstudie.

1. Kies in de ontwerper van de logische app, onder uw trigger, voor **+ nieuwe stap** > **Een actie toevoegen**.

2. Zoek naar ' Maps ' en selecteer deze actie: **Bing Kaarten-route ophalen**

3. Als u geen verbinding met Bing Kaarten hebt, wordt u gevraagd om een verbinding te maken. Geef de details voor deze verbinding op en kies **Maken**.

   ![Selecteer de actie "Bing Kaarten - Route ophalen"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Instelling | Value | Description |
   | ------- | ----- | ----------- |
   | **Verbindingsnaam** | BingMapsConnection | Geef een naam op voor uw verbinding. | 
   | **API-sleutel** | <*your-Bing-Maps-key*> | Voer de sleutel voor Bing Kaarten in die u eerder hebt ontvangen. Leer [hoe u een sleutel kunt verkrijgen](https://msdn.microsoft.com/library/ff428642.aspx) als u geen sleutel voor Bing Kaarten hebt. | 
   | | | |  

4. Wijzig de naam van de actie met deze beschrijving: ```Get route and travel time with traffic```

5. Geef details op voor de actie **Route ophalen** zoals hier wordt weergegeven en beschreven, bijvoorbeeld:

   ![Informatie opgeven voor de actie "Bing Kaarten - Route ophalen"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Instelling | Value | Description |
   | ------- | ----- | ----------- |
   | **Routepunt 1** | <*start-location*> | Het beginpunt van uw route | 
   | **Routepunt 2** | <*end-location*> | De bestemming van uw route | 
   | **Avoid** | Geen | Items die u wilt vermijden op uw route, zoals snelwegen, tolwegen, enzovoort | 
   | **Optimaliseren** | timeWithTraffic | Een parameter voor het optimaliseren van uw route, zoals afstand, reistijd in actuele verkeerssituatie, enzovoort. Selecteer deze parameter: "timeWithTraffic" | 
   | **Afstandseenheid** | <*your-preference*> | De afstandseenheid die voor de route wordt gebruikt. In dit artikel wordt deze eenheid gebruikt: Teller  | 
   | **Vervoermiddel** | Auto | Het vervoermiddel voor uw route. Selecteer deze modus: Verkeer | 
   | **Datum/tijd openbaar vervoer** | Geen | Alleen van toepassing op het vervoermiddel openbaar vervoer | 
   | **Notering datum/tijd** | Geen | Alleen van toepassing op het vervoermiddel openbaar vervoer | 
   |||| 

   Zie [Een route berekenen](https://msdn.microsoft.com/library/ff701717.aspx) voor meer informatie over deze parameters.

6. Sla uw logische app op.

Maak vervolgens een variabele, zodat u de actuele reistijd kunt converteren en opslaan in minuten in plaats van seconden. Zo kunt u voorkomen dat de conversie wordt herhaald en kunt u de waarden eenvoudiger gebruiken in latere stappen. 

## <a name="create-variable-to-store-travel-time"></a>Een variabele maken voor het opslaan van reistijden

Soms wilt u bewerkingen uitvoeren op gegevens in uw werkstroom en de resultaten gebruiken bij latere acties. U kunt variabelen maken die deze resultaten opslaan nadat ze die hebben verwerkt. Zo kunt u de opgeslagen resultaten later gemakkelijk hergebruiken of raadplegen. U kunt alleen variabelen maken in het hoogste niveau van uw logische app.

De hiervoor genoemde actie **Route ophalen** retourneert standaard de actuele reistijd met verkeer binnen enkele seconden via het veld **Reistijd in huidige verkeerssituatie**. Door deze waarde in plaats daarvan te converteren en op te slaan, kunt u de waarde later eenvoudiger hergebruiken zonder dat u opnieuw moet omrekenen.

1. Kies onder de actie **Route ophalen** voor **+ Nieuwe stap** > **Een actie toevoegen**.

2. Zoek naar ' variabelen ' en selecteer deze actie: **Variabelen-variabele initialiseren**

   ![Selecteer de actie "Variabelen - Variabele initialiseren"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

3. Wijzig de naam van deze actie met deze beschrijving: ```Create variable to store travel time```

4. Geef details op voor uw variabele zoals hier wordt beschreven:

   | Instelling | Value | Beschrijving | 
   | ------- | ----- | ----------- | 
   | **Name** | travelTime | De naam van uw variabele | 
   | **Type** | Integer | Het gegevenstype van uw variabele | 
   | **Waarde** | Een expressie die de actuele reistijd van seconden naar minuten converteert (zie de stappen onder deze tabel). | De beginwaarde van uw variabele | 
   |||| 

   1. Als u de expressie voor het veld **Waarde** wilt maken, klikt u in het veld, zodat de lijst met dynamische inhoud wordt weergegeven. 
   Als u de lijst niet ziet, verbreedt u uw browser totdat de lijst wel wordt weergegeven. 
   Kies in de lijst met dynamische inhoud voor **Expressie**. 

      ![Informatie opgeven voor de actie "Variabelen - Variabele initialiseren"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Als u in een paar invoervakken klikt, wordt er een lijst met dynamische inhoud of een inline lijst met parameters weergegeven. Deze lijst geeft parameters van vorige acties weer. U kunt deze gebruiken als invoeren in uw werkstroom. 
      De lijst met dynamische inhoud bevat een expressie-editor waarin u functies kunt selecteren voor het uitvoeren van bewerkingen. 
      Deze expressie-editor wordt alleen weergegeven in de lijst met dynamische inhoud.

      De breedte van uw browser bepaalt welke lijst wordt weergegeven. 
      Als uw browser breed wordt weergegeven, komt de lijst met dynamische inhoud tevoorschijn. 
      Als uw browser smal wordt weergegeven, verschijnt er een inline lijst met parameters onder het invoervak dat momenteel de focus heeft.

   2. Voer deze expressie in in de expressie-editor: ```div(,60)```

      ![Voer deze expressie in "div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   3. Plaats de cursor in de expressie tussen het haakje openen ( **(** ) en de komma ( **,** ). 
   Kies **Dynamische inhoud**.

      ![Plaats de cursor op de juiste plek en kies "Dynamische inhoud"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   4. Selecteer **Reistijd in huidige verkeerssituatie** in de lijst met dynamische inhoud.

      ![Selecteer het veld "Reistijd in huidige verkeerssituatie"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   5. Nadat het veld is omgezet in de expressie kiest u **OK**.

      ![Kies "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Het veld **Waarde** verschijnt nu zoals hier wordt weergegeven:

      ![Het veld 'Waarde' met omgezette expressie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

5. Sla uw logische app op.

Vervolgens voegt u een voorwaarde toe waarmee wordt gecontroleerd of de actuele reistijd een bepaalde limiet overschrijdt.

## <a name="compare-travel-time-with-limit"></a>Reistijd vergelijken met limiet

1. Kies onder de vorige actie voor **+ Nieuwe stap** > **Een voorwaarde toevoegen**. 

2. Wijzig de naam van de voorwaarde met deze beschrijving: ```If travel time exceeds limit```

3. Maak een voorwaarde waarmee wordt gecontroleerd of **travelTime** groter is dan de door u opgegeven limiet zoals hier wordt weergegeven en beschreven:

   1. Klik in de voorwaarde in het vak **Een waarde kiezen** aan de linkerkant (brede browserweergave) of aan de bovenkant (smalle browserweergave).

   2. Selecteer het veld **travelTime** onder **Variabelen** in de lijst met dynamische inhoud of de lijst met parameters.

   3. Selecteer in het vak voor de vergelijking deze operator: **is groter dan**

   4. In het vak **Een waarde kiezen** aan de rechterkant (brede weergave) of aan de onderkant (smalle weergave), voert u deze limiet in: ```15```

   Als u in de smalle weergave werkt, maakt u de voorwaarde als volgt:

   ![Voorwaarde maken in smalle weergave](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time-narrow.png)

4. Sla uw logische app op.

Voeg vervolgens de actie toe die moet worden uitgevoerd als de reistijd uw limiet overschrijdt.

## <a name="send-email-when-limit-exceeded"></a>E-mail verzenden als de limiet wordt overschreden

Voeg nu de actie toe die ervoor zorgt dat u een e-mailbericht ontvangt als de reistijd uw limiet overschrijdt. Dit e-mailbericht bevat de actuele reistijd en de extra tijd die nodig is om de opgegeven route af te leggen. 

1. In de vertakking **Indien waar** van de voorwaarde kiest u **Een actie toevoegen**.

2. Zoek naar 'e-mailbericht verzenden' en selecteer de e-mailconnector en de actie voor 'e-mailbericht verzenden' die u wilt gebruiken.

   ![Zoek en selecteer de actie "e-mailbericht verzenden"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Selecteer **Outlook.com** voor persoonlijke Microsoft-accounts. 
   * Selecteer **Office 365 Outlook** voor werk- of schoolaccounts van Azure.

3. Als u nog geen verbinding hebt, wordt u gevraagd om u aan te melden bij uw e-mailaccount.

   Logic Apps maakt een verbinding met uw e-mailaccount.

4. Wijzig de naam van de actie met deze beschrijving: ```Send email with travel time```

5. Voer het e-mailadres van de ontvanger in het vak **Aan** in. Voor testdoeleinden dient u uw e-mailadres te gebruiken.

6. Geef het onderwerp van het e-mailbericht op in het vak **Onderwerp** en sluit de variabele **travelTime** in.

   1. Voer de tekst in ```Current travel time (minutes):``` met een spatie aan het eind. 
   
   2. Selecteer **travelTime** onder **Variabelen** in de lijst met parameters of de lijst met dynamische inhoud. 
   
      Als u browser bijvoorbeeld in een kleine weergave wordt weergegeven:

      ![Voer het onderwerp in plus de expressie die de reistijd retourneert](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-subject-settings.png)

7. Geef de inhoud van de hoofdtekst van de e-mail op in het vak **Hoofdtekst**. 

   1. Voer de tekst in ```Add extra travel time (minutes):``` met een spatie aan het eind. 
   
   2. Als u de lijst niet ziet, verbreedt u uw browser totdat de lijst met dynamische inhoud wel wordt weergegeven. 
   Kies in de lijst met dynamische inhoud voor **Expressie**.

      ![Expressie maken voor hoofdtekst van e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   3. Voer deze expressie in in de expressie-editor, zodat u het aantal minuten waarmee de limiet wordt overschreden, kunt berekenen: ```sub(,15)```

      ![Voer de expressie in voor het berekenen van de extra reistijd in minuten](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   4. Plaats de cursor in de expressie tussen het haakje openen ( **(** ) en de komma ( **,** ). Kies **Dynamische inhoud**.

      ![Ga verder met het maken van de expressie voor het berekenen van de extra reistijd in minuten](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   5. Selecteer onder **Variabelen** **travelTime**.

      ![Selecteer het veld 'travelTime' dat in de expressie wordt gebruikt](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   6. Nadat het veld is omgezet in de expressie kiest u **OK**.

      ![Het veld 'Hoofdtekst' met omgezette expressie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Het veld **Hoofdtekst** verschijnt nu zoals hier wordt weergegeven:

      ![Het veld 'Hoofdtekst' met omgezette expressie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

8. Sla uw logische app op.

Test vervolgens uw logische app, die er nu bijna net zo uitziet als dit voorbeeld:

![Voltooide logische app](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

Kies **Uitvoeren** op de werkbalk in de ontwerper als u de logische app handmatig wilt uitvoeren. Als de actuele reistijd onder uw limiet blijft, zal uw logische app verder niets doen en wacht die met het uitvoeren van een nieuwe controle tot het volgende interval.
Maar als de actuele reistijd de limiet overschrijdt, krijgt u een e-mailbericht met de actuele reistijd en het aantal minuten waarmee de limiet wordt overschreden. Hier ziet u een voorbeeld van een e-mail die uw logische app verstuurt:

![E-mailbericht verzonden met reistijd](./media/tutorial-build-scheduled-recurring-logic-app-workflow/email-notification.png)

Als u geen een e-mailberichten ontvangt, controleert u de map met ongewenste e-mail. Het is mogelijk dat uw filter voor ongewenste e-mail dit soort e-mails in deze map zet. Als u niet zeker weet of uw logische app correct wordt uitgevoerd, kunt u [Problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md) raadplegen.

Gefeliciteerd, u hebt nu een herhalende logische app op basis van een planning gemaakt en uitgevoerd. 

Als u andere logische apps wilt maken die de trigger **Planning - Terugkeerpatroon** gebruiken, kunt u deze sjablonen gebruiken, die beschikbaar worden nadat u een logische app hebt gemaakt:

* Dagelijkse herinneringen per e-mail ontvangen.
* Oudere Azure-blobs verwijderen.
* Een bericht toevoegen aan een wachtrij van Azure Storage.

## <a name="clean-up-resources"></a>Resources opschonen

Als u die niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat. Ga in het Azure-hoofdmenu naar **Resourcegroepen** en selecteer de resourcegroep voor uw logische app. Kies **Resourcegroep verwijderen**. Voer ter bevestiging de naam van de resourcegroep in en kies **Verwijderen**.

!["Overzicht" > "Resourcegroep verwijderen"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een logische app gemaakt die verkeer controleert op basis van een opgegeven planning (‘s ochtends op doordeweekse dagen) en die actie onderneemt (e-mails verzenden) wanneer de reistijd een bepaalde limiet overschrijdt. Leer nu hoe u een logische app maakt waarmee u aanmeldingen voor een mailinglijst indient ter goedkeuring. Hiervoor gebruikt u een integratie van Azure-services, Microsoft-services en andere SaaS-apps.

> [!div class="nextstepaction"]
> [Mailinglijstaanvragen beheren](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
