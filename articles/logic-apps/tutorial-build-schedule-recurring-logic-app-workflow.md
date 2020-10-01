---
title: Geautomatiseerde op planning gebaseerde werkstromen bouwen
description: 'Zelfstudie: een terugkerende, geautomatiseerde werkstroom maken op basis van een planning met behulp van Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 8c9239196d26bcd4967b685fa7970c4d3bd706d4
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030528"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Zelfstudie: Geautomatiseerde op een planning gebaseerde workflows maken met behulp van Azure Logic Apps

Deze zelfstudie laat zien hoe u een [logic app](../logic-apps/logic-apps-overview.md) kunt bouwen en een terugkerende workflow kunt automatiseren die volgens een planning draait. Dit voorbeeld van een logische app wordt elke doordeweekse ochtend uitgevoerd en controleert de reistijd, inclusief het verkeer, tussen twee plaatsen. Als de tijd een bepaalde limiet overschrijdt, verzendt de logische app een e-mail met de reistijd en de extra tijd die u nodig hebt om uw bestemming te bereiken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een lege, logische app maken.
> * Voeg de trigger Terugkeerpatroon toe die de planning voor uw logische app specificeert.
> * Een Bing Maps-actie toevoegen die de reistijd voor een route ophaalt.
> * Een actie toevoegen die een variabele maakt, de reistijd converteert van seconden naar minuten en dat resultaat opslaat in de variabele.
> * Een voorwaarde toevoegen die de reistijd vergelijkt met een opgegeven limiet.
> * Een actie toevoegen die u een e-mailbericht stuurt als de reistijd de limiet overschrijdt.

Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Overzicht van een logische app-werkstroom op hoog niveau](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [moet u zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

* Een e-mailaccount van een e-mailprovider die door Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](/connectors/). In deze snelstartgids wordt gebruikgemaakt van een werk- of schoolaccount. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

  > [!IMPORTANT]
  > Als u de Gmail-connector wilt gebruiken, kunnen alleen bedrijfsaccounts van G Suite deze connector zonder beperking in logische apps gebruiken. Als u een Gmail-consumentenaccount hebt, kunt u deze connector alleen gebruiken met specifieke door Google goedgekeurde services, of u kunt [een Google-client-app maken voor verificatie bij uw Gmail-connector](/connectors/gmail/#authentication-and-bring-your-own-application). Zie [Beleid voor gegevensbeveiliging en privacybeleid voor Google-connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md) voor meer informatie.

* Als u de reistijd voor een route wilt ophalen, hebt u een toegangssleutel nodig voor de API van Bing Kaarten. Volg de stappen in [Een Bing Kaarten-sleutel krijgen](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) om deze sleutel te krijgen.

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Selecteer in het hoofdmenu van Azure **Een resource maken** > **Integratie** > **Logische app**.

   ![Uw logische app-resource maken](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. In het menu **Logische app maken** geeft u de informatie over uw logische app op zoals hier wordt weergegeven en beschreven. Als u gereed bent, selecteert u **Maken**.

   ![Geef informatie op over uw logische app](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | LA-TravelTime | De naam van uw logische app mag alleen letters, cijfers, afbreekstreepjes (`-`), onderstrepingstekens (`_`), haakjes (`(`, `)`) en punten (`.`) bevatten. In dit voorbeeld wordt LA-TravelTime gebruikt. |
   | **Abonnement** | <*your-Azure-subscription-name*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | LA-TravelTime-RG | De naam van de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) die wordt gebruikt om verwante resources te organiseren. In dit voorbeeld wordt LA-TravelTime-RG gebruikt. |
   | **Locatie** | VS - west | De regio waarin informatie over uw logische app moet worden opgeslagen. In dit voorbeeld wordt 'US - west' gebruikt. |
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u in de Azure-werkbalk de optie **Meldingen** > **Resources openen** voor uw geïmplementeerde logische app.

   ![Ga naar uw nieuwe logische app-resource](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   U kunt ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps-ontwerpfunctie wordt geopend en u ziet een pagina met een inleidende video, veelgebruikte triggers en patronen voor logische apps. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Selecteer een leeg sjabloon voor uw logische app](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Voeg vervolgens de [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) Terugkeerpatroon toe, die op basis van een opgegeven planning wordt geactiveerd. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer nieuwe gegevens voldoen aan een bepaalde voorwaarde. Bekijk [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie.

## <a name="add-the-recurrence-trigger"></a>De trigger Terugkeerpatroon toevoegen

1. Voer in de ontwerpfunctie van de logische app in het zoekvenster terugkeerpatroon in als uw filter. Selecteer in de lijst **Triggers** de trigger **Terugkeerpatroon**.

   ![De trigger Terugkeerpatroon toevoegen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Selecteer op de vorm **Terugkeerpatroon** de knop met het **beletselteken** ( **...** ) en selecteer **Naam wijzigen**. Wijzig de naam van de trigger met deze beschrijving: `Check travel time every weekday morning`

   ![De naam van de beschrijving van de trigger Terugkeerpatroon wijzigen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Wijzig in de trigger deze eigenschappen.

   ![Het interval en de frequentie van de trigger van het terugkeerpatroon wijzigen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Interval** | Ja | 1 | Het aantal intervallen dat tussen controles moet worden gewacht |
   | **Frequentie** | Ja | Wekelijks | Tijdseenheid die voor het terugkeerpatroon wordt gebruikt |
   |||||

1. Open onder **Interval** en **Frequentie**de lijst **Nieuwe parameter toevoegen** en selecteer deze eigenschappen om aan de trigger toe te voegen.

   * **Deze dagen**
   * **Deze uren**
   * **Deze minuten**

   ![Eigenschappen voor de trigger Terugkeerpatroon toevoegen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Stel nu de waarden voor de extra eigenschappen in zoals hier wordt weergegeven en beschreven.

   ![Gegevens van de planning en het terugkeerpatroon opgeven](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Deze dagen** | Maandag, dinsdag, woensdag, donderdag, vrijdag | Alleen beschikbaar wanneer **Frequentie** is ingesteld op "Wekelijks" |
   | **Deze uren** | 7, 8, 9 | Alleen beschikbaar wanneer **Frequentie** is ingesteld op 'Wekelijks' of 'Dagelijks'. Selecteer de uren van de dag waarop dit terugkeerpatroon wordt uitgevoerd. Dit voorbeeld wordt uitgevoerd om 7, 8 en 9 uur. |
   | **Deze minuten** | 0, 15, 30, 45 | Alleen beschikbaar wanneer **Frequentie** is ingesteld op 'Wekelijks' of 'Dagelijks'. Selecteer de minuten van de dag waarop dit terugkeerpatroon wordt uitgevoerd. Dit voorbeeld wordt iedere 15 minuten na het hele uur uitgevoerd. |
   ||||

   Deze trigger wordt iedere doordeweekse dag om de 15 minuten uitgevoerd tussen 07:00 en 09:45 uur. Het vak **Preview** toont de terugkeerplanning. Zie [Taken en werkstromen plannen](../connectors/connectors-native-recurrence.md) en [Werkstroomacties en -triggers](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) voor meer informatie.

1. Als u de details van de trigger voorlopig wilt verbergen, klikt u in de titelbalk van de vorm.

   ![Shape samenvouwen om details te verbergen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Uw logische app is nu live, maar doet niets anders dan herhalen. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="get-the-travel-time-for-a-route"></a>De reistijd voor een route ophalen

Nu u een trigger hebt, voegt u een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die de reistijd tussen twee locaties ophaalt. Logic Apps biedt een connector voor de API van Bing Kaarten. Hiermee kunt u deze informatie eenvoudig ophalen. Voordat u deze taak start, moet u ervoor zorgen dat u een API-sleutel van Bing Kaarten hebt, zoals beschreven in de vereisten in deze zelfstudie.

1. Selecteer in de ontwerpfunctie van de logische app onder uw trigger de optie **Nieuwe stap**.

1. Selecteer onder **Kies een actie**de optie **Standaard**. Voer in het zoekvenster bing maps in als uw filter en selecteer de actie **Route ophalen**.

   ![Selecteer de actie Route ophalen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Als u geen verbinding met Bing Kaarten hebt, wordt u gevraagd om een verbinding te maken. Geef de details voor deze verbinding op en selecteer **Maken**.

   ![Verbinding maken met Bing Maps-API](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Verbindingsnaam** | Ja | BingMapsConnection | Geef een naam op voor uw verbinding. In dit voorbeeld wordt BingMapsConnection gebruikt. |
   | **API-sleutel** | Ja | <*your-Bing-Maps-key*> | Voer de sleutel voor Bing Kaarten in die u eerder hebt ontvangen. Leer [hoe u een sleutel kunt verkrijgen](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) als u geen sleutel voor Bing Kaarten hebt. |
   |||||

1. Wijzig de naam van de actie met deze beschrijving: `Get route and travel time with traffic`

1. Open in de actie de optie **Nieuwe parameterlijst toevoegen** en selecteer deze eigenschappen om aan de actie toe te voegen.

   * **Optimaliseren**
   * **Afstandseenheid**
   * **Vervoermiddel**

   ![Eigenschappen toevoegen aan de actie Route ophalen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Stel nu de waarden voor de eigenschappen van de actie in zoals hier wordt weergegeven en beschreven.

   ![Geef de details op voor de actie Route ophalen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Routepunt 1** | Ja | <*start-location*> | Het beginpunt van uw route |
   | **Routepunt 2** | Ja | <*end-location*> | De bestemming van uw route |
   | **Optimaliseren** | Nee | timeWithTraffic | Een parameter voor het optimaliseren van uw route, zoals afstand, reistijd in actuele verkeerssituatie, enzovoort. Selecteer de parameter timeWithTraffic. |
   | **Afstandseenheid** | Nee | <*your-preference*> | De afstandseenheid die voor de route wordt gebruikt. In dit voor beeld wordt Mijl gebruikt als eenheid. |
   | **Vervoermiddel** | Nee | Auto | Het vervoermiddel voor uw route. Selecteer de modus Auto. |
   ||||

   Zie [Een route berekenen](/bingmaps/rest-services/routes/calculate-a-route) voor meer informatie over deze parameters.

1. Sla uw logische app op.

Maak vervolgens een variabele, zodat u de actuele reistijd kunt converteren en opslaan in minuten in plaats van seconden. Zo kunt u voorkomen dat de conversie wordt herhaald en kunt u de waarden eenvoudiger gebruiken in latere stappen. 

## <a name="create-a-variable-to-store-travel-time"></a>Een variabele maken voor het opslaan van reistijden

Soms wilt u bewerkingen uitvoeren op gegevens in uw werkstroom en de resultaten gebruiken bij latere acties. U kunt variabelen maken die deze resultaten opslaan nadat ze die hebben verwerkt. Zo kunt u de opgeslagen resultaten later gemakkelijk hergebruiken of raadplegen. U kunt alleen variabelen maken in het hoogste niveau van uw logische app.

De hiervoor genoemde actie **Route ophalen** retourneert standaard de actuele reistijd met verkeer binnen enkele seconden via de eigenschap **Reistijd in huidige verkeerssituatie**. Door deze waarde in plaats daarvan te converteren en op te slaan, kunt u de waarde later eenvoudiger hergebruiken zonder dat u opnieuw moet omrekenen.

1. Selecteer onder de actie **Route ophalen** de optie **Nieuwe stap**.

1. Selecteer onder **Kies een actie**de optie **Ingebouwd**. Voer in het zoekvak variabelen in en selecteer de actie **Variabele initialiseren**.

   ![Selecteer de actie Variabele initialiseren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Wijzig de naam van deze actie met deze beschrijving: `Create variable to store travel time`

1. Geef details op voor uw variabele zoals hier wordt beschreven:

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Naam** | Ja | travelTime | De naam van uw variabele. In dit voorbeeld wordt travelTime gebruikt. |
   | **Type** | Ja | Geheel getal | Het gegevenstype van uw variabele |
   | **Waarde** | Nee| Een expressie die de actuele reistijd van seconden naar minuten converteert (zie de stappen onder deze tabel). | De beginwaarde van uw variabele |
   ||||

   1. Als u de expressie voor de eigenschap **Waarde** wilt maken, klikt u in het vak, zodat de lijst met dynamische inhoud wordt weergegeven. Als u de lijst niet ziet, verbreedt u uw browser totdat de lijst wel wordt weergegeven. Selecteer in de lijst met dynamische inhoud de optie **Expressie**.

      ![Geef informatie op voor de actie Variabele initialiseren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Wanneer u in een aantal bewerkingsvelden klikt, verschijnt de dynamische inhoudslijst. Deze lijst geeft eigenschappen van vorige acties weer. U kunt deze gebruiken als invoeren in uw werkstroom. De lijst met dynamische inhoud bevat een expressie-editor waarin u functies kunt selecteren voor het uitvoeren van bewerkingen. Deze expressie-editor wordt alleen weergegeven in de lijst met dynamische inhoud.

   1. Voer deze expressie in in de expressie-editor: `div(,60)`

      ![Voer deze expressie in "div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Plaats de cursor in de expressie tussen het haakje openen ( **(** ) en de komma ( **,** ). 
   Selecteer **Dynamische inhoud**.

      ![Plaats de cursor op de juiste plek en selecteer Dynamische inhoud](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Selecteer **Reistijd in huidige verkeerssituatie** in de lijst met dynamische inhoud.

      ![Selecteer de eigenschap Reistijd in huidige verkeerssituatie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Nadat de eigenschapswaarde in de expressie is omgezet, selecteert u **OK**.

      ![Selecteer OK om de expressie te maken.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      De eigenschap **Waarde** wordt nu zoals hier weergegeven:

      ![De eigenschap Value wordt weergegeven met de omgezette expressie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Sla uw logische app op.

Vervolgens voegt u een voorwaarde toe waarmee wordt gecontroleerd of de actuele reistijd een bepaalde limiet overschrijdt.

## <a name="compare-the-travel-time-with-limit"></a>Vergelijk de reistijd vergelijken met de limiet

1. Selecteer onder de vorige actie **Nieuwe stap**.

1. Selecteer onder **Kies een actie**de optie **Ingebouwd**. Voer in het zoekvak voorwaarde in als uw filter. Selecteer in de lijst met acties de actie **Voorwaarde**.

   ![Selecteer de actie Voorwaarde](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Wijzig de naam van de voorwaarde met deze beschrijving: `If travel time exceeds limit`

1. Maak een voorwaarde waarmee wordt gecontroleerd of de eigenschapswaarde **travelTime** groter is dan de door u opgegeven limiet zoals hier wordt beschreven en weergegeven:

   1. Klik in de voorwaarde op het vakje **Kies een waarde** aan de linkerkant van de voorwaarde.

   1. Selecteer in de lijst met dynamische inhoud onder **Variabelen** de eigenschap **travelTime**.

      ![Maak de linkerkant van de voorwaarde](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Selecteer in het middelste vergelijkingsvak de operator **is groter dan**.

   1. Voer in het vak **Kies een waarde** aan de rechterkant van de voorwaarde deze limiet in: `15`

      Wanneer u klaar bent, ziet de voorwaarde eruit zoals in dit voorbeeld:

      ![Voltooide voorwaarde voor controleren van reistijd](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Sla uw logische app op.

Voeg vervolgens de actie toe die moet worden uitgevoerd als de reistijd uw limiet overschrijdt.

## <a name="send-email-when-limit-exceeded"></a>E-mail verzenden als de limiet wordt overschreden

Voeg nu de actie toe die ervoor zorgt dat u een e-mailbericht ontvangt als de reistijd uw limiet overschrijdt. Dit e-mailbericht bevat de actuele reistijd en de extra tijd die nodig is om de opgegeven route af te leggen.

1. Selecteer in de vertakking**Indien waar** van de voorwaarde de optie **Een actie toevoegen**.

1. Selecteer onder **Kies een actie**de optie **Standaard**. Voer in het zoekvak e-mail verzenden in. De lijst levert veel resultaten op, dus selecteer eerst de gewenste e-mailconnector, bijvoorbeeld:

   ![Zoek en selecteer de e-mailconnector die u wilt gebruiken](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Selecteer **Office 365 Outlook** voor werk- of schoolaccounts van Azure.
   * Selecteer **Outlook.com** voor persoonlijke Microsoft-accounts.

1. Wanneer de acties van de connector worden weergegeven, selecteert u e-mailactie verzenden die u wilt gebruiken, bijvoorbeeld:

   ![De actie 'Een e-mail verzenden' selecteren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Als u nog geen verbinding hebt, wordt u gevraagd om u aan te melden bij uw e-mailaccount.

   Logic Apps maakt een verbinding met uw e-mailaccount.

1. Wijzig de naam van de actie met deze beschrijving: `Send email with travel time`

1. Voer het e-mailadres van de ontvanger in het vak **Aan** in. Voor testdoeleinden dient u uw e-mailadres te gebruiken.

1. Geef het onderwerp van het e-mailbericht op in het vak **Onderwerp** en sluit de variabele **travelTime** in.

   1. Voer de tekst in `Current travel time (minutes):` met een spatie aan het eind. 

   1. Selecteer in de lijst met dynamische inhoud onder **Variabelen** de optie **Meer weergeven**.

      ![Zoek de variabele travelTime](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Nadat **travelTime** onder **Variables** is verschenen, selecteert u **travelTime**.

      ![Voer het onderwerp in plus de expressie die de reistijd retourneert](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Geef de inhoud van de hoofdtekst van de e-mail op in het vak **Hoofdtekst**.

   1. Voer de tekst in `Add extra travel time (minutes):` met een spatie aan het eind.

   1. Selecteer in de lijst met dynamische inhoud de optie **Expressie**.

      ![Expressie maken voor hoofdtekst van e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. Voer deze expressie in in de expressie-editor, zodat u het aantal minuten waarmee de limiet wordt overschreden, kunt berekenen: ```sub(,15)```

      ![Voer de expressie in voor het berekenen van de extra reistijd in minuten](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Plaats de cursor in de expressie tussen het haakje openen ( **(** ) en de komma ( **,** ). Selecteer **Dynamische inhoud**.

      ![Ga verder met het maken van de expressie voor het berekenen van de extra reistijd in minuten](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Selecteer onder **Variabelen** **travelTime**.

      ![Selecteer de eigenschap travelTime die in de expressie moet worden gebruikt](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Nadat de eigenschap in de expressie is omgezet, selecteert u **OK**.

      ![Nadat de eigenschap Hoofdtekst is opgelost, selecteert u OK](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      De eigenschap **Hoofdtekst** wordt nu zoals hier weergegeven:

      ![Opgeloste eigenschap Hoofdtekst in expressie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Sla uw logische app op.

Test vervolgens uw logische app, die er nu bijna net zo uitziet als dit voorbeeld:

![Voorbeeld van een voltooide logische app-werkstroom](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

Selecteer **Uitvoeren** op de werkbalk in de ontwerper als u de logische app handmatig wilt uitvoeren.

* Als de actuele reistijd onder uw limiet blijft, zal uw logische app verder niets doen en wacht die met het uitvoeren van een nieuwe controle tot het volgende interval. 

* Als de actuele reistijd de limiet overschrijdt, krijgt u een e-mailbericht met de actuele reistijd en het aantal minuten waarmee de limiet wordt overschreden. Hier ziet u een voorbeeld van een e-mail die uw logische app verstuurt:

![Voorbeeld van een e-mailbericht waarin de reistijd wordt weergegeven](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Als u geen een e-mailberichten ontvangt, controleert u de map met ongewenste e-mail. Het is mogelijk dat uw filter voor ongewenste e-mail dit soort e-mails in deze map zet. Als u niet zeker weet of uw logische app correct wordt uitgevoerd, kunt u [Problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md) raadplegen.

Gefeliciteerd, u hebt nu een herhalende logische app op basis van een planning gemaakt en uitgevoerd. 

Als u andere logische apps wilt maken die de trigger **Terugkeerpatroon** gebruiken, kunt u deze sjablonen gebruiken, die beschikbaar worden nadat u een logische app hebt gemaakt:

* Dagelijkse herinneringen per e-mail ontvangen.
* Oudere Azure-blobs verwijderen.
* Een bericht toevoegen aan een wachtrij van Azure Storage.

## <a name="clean-up-resources"></a>Resources opschonen

Als u het voorbeeld van de logische app niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat. 

1. Ga in het Azure-hoofdmenu naar **Resourcegroepen** en selecteer de resourcegroep voor uw logische app.

1. Selecteer in het menu van de resourcegroep de optie **Overzicht** > **Resourcegroep verwijderen**. 

   !["Overzicht" > "Resourcegroep verwijderen"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Voer ter bevestiging de naam van de resourcegroep in en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een logische app gemaakt die verkeer controleert op basis van een opgegeven planning (‘s ochtends op doordeweekse dagen) en die actie onderneemt (e-mails verzenden) wanneer de reistijd een bepaalde limiet overschrijdt. Leer nu hoe u een logische app maakt waarmee u aanmeldingen voor een mailinglijst indient ter goedkeuring. Hiervoor gebruikt u een integratie van Azure-services, Microsoft-services en andere SaaS-apps.

> [!div class="nextstepaction"]
> [Mailinglijstaanvragen beheren](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
