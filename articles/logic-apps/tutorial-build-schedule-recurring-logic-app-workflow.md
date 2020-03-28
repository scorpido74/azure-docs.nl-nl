---
title: Op planning gebaseerde geautomatiseerde werkstromen bouwen
description: Zelfstudie - Een op planning gebaseerde, terugkerende, geautomatiseerde werkstroom maken met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 17802228c8f08e3c8f1533296e2d39080f6f8b7a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75456617"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Zelfstudie: Geautomatiseerde, op planning gebaseerde, terugkerende werkstromen maken met Azure Logic Apps

In deze zelfstudie ziet u hoe u een [logische app](../logic-apps/logic-apps-overview.md) maken en een terugkerende werkstroom automatiseren die volgens een planning wordt uitgevoerd. Specifiek, dit voorbeeld logica app draait elke weekdag 's ochtends en controleert de reistijd, met inbegrip van het verkeer, tussen twee plaatsen. Als de tijd een bepaalde limiet overschrijdt, verzendt de logische app een e-mail met de reistijd en de extra tijd die u nodig hebt om uw bestemming te bereiken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een lege, logische app maken.
> * Voeg een recidieftrigger toe die de planning voor uw logische app opgeeft.
> * Voeg een actie van Bing Maps toe die de reistijd voor een route krijgt.
> * Voeg een actie toe die een variabele maakt, de reistijd omzet van seconden naar minuten en die resulteren in de variabele.
> * Een voorwaarde toevoegen die de reistijd vergelijkt met een opgegeven limiet.
> * Voeg een actie toe die u een e-mail stuurt als de reistijd de limiet overschrijdt.

Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Overzicht van de werkstroom van logische apps op hoog niveau](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement hebt, [meldt u zich aan voor een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

* Een e-mailaccount van een e-mailprovider die wordt ondersteund door Logic Apps, zoals Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](https://docs.microsoft.com/connectors/). Deze quickstart maakt gebruik van een Office 365 Outlook-account. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen hetzelfde, maar kan uw gebruikersinterface enigszins verschillen.

* Als u de reistijd voor een route wilt ophalen, hebt u een toegangssleutel nodig voor de API van Bing Kaarten. Volg de stappen in [Een Bing Kaarten-sleutel krijgen](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) om deze sleutel te krijgen.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Selecteer in het hoofdmenu van Azure de optie Een > **logica-app**voor > **bronintegratie** **maken**.

   ![Uw logische app-bron maken](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. In het menu **Logische app maken** geeft u de informatie over uw logische app op zoals hier wordt weergegeven en beschreven. Als u gereed bent, selecteert u **Maken**.

   ![Informatie geven over uw logische app](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | LA-TravelTime | De naam van uw logische app, die alleen letters,`-`cijfers,`_`koppeltekens (`(`), `)`onderstreept (`.`), haakjes ( , en perioden ( ). In dit voorbeeld wordt "LA-TravelTime" gebruikt. |
   | **Abonnement** | <*naam van uw Azure-abonnement*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | LA-TravelTime-RG | De naam voor de [Azure-brongroep](../azure-resource-manager/management/overview.md), die wordt gebruikt om gerelateerde resources te ordenen. In dit voorbeeld wordt "LA-TravelTime-RG" gebruikt. |
   | **Locatie** | VS - west | THet gebied waar u uw logica-app-informatie opslaan. In dit voorbeeld wordt "West US" gebruikt. |
   | **Logboekanalyse** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u op de werkbalk Azure de optie **Meldingen** > **Ga naar bron** voor uw geïmplementeerde logica-app.

   ![Naar uw nieuwe logic app-bron gaan](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   U ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps Designer opent en toont een pagina met een introductievideo en veelgebruikte triggers en logische app-patronen. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Sjabloon voor lege logica-apps selecteren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Voeg vervolgens de [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts)recidieftrigger toe, die wordt geactiveerd op basis van een opgegeven schema. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer nieuwe gegevens voldoen aan een bepaalde voorwaarde. Bekijk [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie.

## <a name="add-the-recurrence-trigger"></a>De recidieftrigger toevoegen

1. Voer in het zoekvak 'herhaling' in in het zoekvak als filter. Selecteer in de lijst **Triggers** de **recidieftrigger.**

   ![Trigger 'Herhaling' toevoegen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Selecteer in de vorm **Herhaling** de knop **ellips** (**... )** en selecteer De **naam wijzigen**. Wijzig de naam van de trigger met deze beschrijving: `Check travel time every weekday morning`

   ![De naam van de beschrijving van de recidieftrigger wijzigen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Verander deze eigenschappen in de trigger.

   ![Het interval en de frequentie van de Recidief-trigger wijzigen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Interval** | Ja | 1 | Het aantal intervallen dat tussen controles moet worden gewacht |
   | **Frequentie** | Ja | Wekelijks | Tijdseenheid die voor het terugkeerpatroon wordt gebruikt |
   |||||

1. Open **onder Interval** en **Frequentie**de lijst Nieuwe **parameter toevoegen** en selecteer deze eigenschappen die u aan de trigger wilt toevoegen.

   * **Deze dagen**
   * **Deze uren**
   * **Deze minuten**

   ![Eigenschappen toevoegen voor de trigger voor Herhaling](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Stel nu de waarden in voor de extra eigenschappen zoals hier weergegeven en beschreven.

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

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

Uw logische app is nu live, maar doet niets anders dan herhalen. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="get-the-travel-time-for-a-route"></a>De reistijd voor een route ophalen

Nu u een trigger hebt, voegt u een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die de reistijd tussen twee locaties ophaalt. Logic Apps biedt een connector voor de API van Bing Kaarten. Hiermee kunt u deze informatie eenvoudig ophalen. Voordat u deze taak start, moet u ervoor zorgen dat u een API-sleutel van Bing Kaarten hebt, zoals beschreven in de vereisten in deze zelfstudie.

1. Selecteer in de Logic App Designer onder uw trigger de optie **Nieuwe stap**.

1. Selecteer Onder **Een actie kiezen**de optie **Standaard**. Voer in het zoekvak 'bing-kaarten' in als filter en selecteer de actie **Route downloaden.**

   ![Selecteer actie 'Route opdoen'](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Als u geen verbinding met Bing Kaarten hebt, wordt u gevraagd om een verbinding te maken. Geef deze verbindingsgegevens op en selecteer **Maken**.

   ![Verbinding maken met De API van Bing Maps](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Verbindingsnaam** | Ja | BingMapsConnection | Geef een naam op voor uw verbinding. In dit voorbeeld wordt gebruik gemaakt van "BingMapsConnection". |
   | **API-sleutel** | Ja | <*your-Bing-Maps-toets*> | Voer de sleutel voor Bing Kaarten in die u eerder hebt ontvangen. Leer [hoe u een sleutel kunt verkrijgen](https://msdn.microsoft.com/library/ff428642.aspx) als u geen sleutel voor Bing Kaarten hebt. |
   |||||

1. Wijzig de naam van de actie met deze beschrijving: `Get route and travel time with traffic`

1. Open binnen de actie de **lijst Nieuwe parameter toevoegen**en selecteer deze eigenschappen die u aan de actie wilt toevoegen.

   * **Optimaliseren**
   * **Afstandseenheid**
   * **Vervoermiddel**

   ![Eigenschappen toevoegen aan actie 'Route opdoen'](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Stel nu de waarden in voor de eigenschappen van de actie zoals hier weergegeven en beschreven.

   ![Details opgeven voor de actie 'Route verzamelen'](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Routepunt 1** | Ja | <*startlocatie*> | Het beginpunt van uw route |
   | **Routepunt 2** | Ja | <*eindlocatie*> | De bestemming van uw route |
   | **Optimaliseren** | Nee | timeWithTraffic | Een parameter voor het optimaliseren van uw route, zoals afstand, reistijd in actuele verkeerssituatie, enzovoort. Selecteer de parameter "timeWithTraffic". |
   | **Afstandseenheid** | Nee | <*uw voorkeur*> | De afstandseenheid die voor de route wordt gebruikt. In dit voorbeeld wordt "Mile" als eenheid gebruikt. |
   | **Vervoermiddel** | Nee | Auto | Het vervoermiddel voor uw route. Selecteer de modus 'Rijden'. |
   ||||

   Zie [Een route berekenen](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route) voor meer informatie over deze parameters.

1. Sla uw logische app op.

Maak vervolgens een variabele, zodat u de actuele reistijd kunt converteren en opslaan in minuten in plaats van seconden. Zo kunt u voorkomen dat de conversie wordt herhaald en kunt u de waarden eenvoudiger gebruiken in latere stappen. 

## <a name="create-a-variable-to-store-travel-time"></a>Een variabele maken om reistijd op te slaan

Soms wilt u bewerkingen uitvoeren op gegevens in uw werkstroom en vervolgens de resultaten gebruiken in latere acties. U kunt variabelen maken die deze resultaten opslaan nadat ze die hebben verwerkt. Zo kunt u de opgeslagen resultaten later gemakkelijk hergebruiken of raadplegen. U kunt alleen variabelen maken in het hoogste niveau van uw logische app.

Standaard retourneert de vorige routeactie **Ophalen** de huidige reistijd met verkeer in enkele seconden van de eigenschap Verkeer van **reisduur.** Door deze waarde in plaats daarvan te converteren en op te slaan, kunt u de waarde later eenvoudiger hergebruiken zonder dat u opnieuw moet omrekenen.

1. Selecteer onder de actie **Route opdoen** de optie **Nieuwe stap**.

1. Selecteer **Ingebouwde**actie onder **Een actie kiezen**. Voer in het zoekvak 'variabelen' in en selecteer de variabele actie **Initialiseren.**

   ![De actie 'Variabele initialiseren' selecteren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Wijzig de naam van deze actie met deze beschrijving: `Create variable to store travel time`

1. Geef details op voor uw variabele zoals hier wordt beschreven:

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Naam** | Ja | travelTime | De naam voor uw variabele. In dit voorbeeld wordt gebruik gemaakt van "travelTime". |
   | **Type** | Ja | Geheel getal | Het gegevenstype van uw variabele |
   | **Waarde** | Nee| Een expressie die de actuele reistijd van seconden naar minuten converteert (zie de stappen onder deze tabel). | De beginwaarde van uw variabele |
   ||||

   1. Als u de expressie voor de eigenschap **Waarde** wilt maken, klikt u in het vak zodat de lijst met dynamische inhoud wordt weergegeven. Als u de lijst niet ziet, verbreedt u uw browser totdat de lijst wel wordt weergegeven. Selecteer **Expressie**in de lijst met dynamische inhoud .

      ![Informatie verstrekken voor de actie "Initialiseren variabele"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Wanneer u in sommige bewerkingsvakken klikt, wordt de lijst met dynamische inhoud weergegeven. In deze lijst worden alle eigenschappen van eerdere acties weergegeven die u als invoer in uw werkstroom gebruiken. De lijst met dynamische inhoud heeft een expressie-editor waar u functies selecteren om bewerkingen uit te voeren. Deze expressie-editor wordt alleen weergegeven in de lijst met dynamische inhoud.

   1. Voer deze expressie in in de expressie-editor: `div(,60)`

      ![Voer deze expressie in "div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Plaats de cursor in de expressie tussen het haakje openen (**(**) en de komma (**,**). 
   selecteer **Dynamische inhoud**.

      ![Positiecursor, selecteer 'Dynamische inhoud'](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Selecteer **Reistijd in huidige verkeerssituatie** in de lijst met dynamische inhoud.

      ![Selecteer de eigenschap Verkeer van reisduur](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Nadat de eigenschapswaarde in de expressie is opgelost, selecteert u **OK**.

      ![Als u de expressie wilt voltooien, selecteert u 'OK'](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      De eigenschap **Waarde** wordt nu weergegeven zoals hier wordt weergegeven:

      ![De eigenschap 'Waarde' wordt weergegeven met opgeloste expressie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Sla uw logische app op.

Vervolgens voegt u een voorwaarde toe waarmee wordt gecontroleerd of de actuele reistijd een bepaalde limiet overschrijdt.

## <a name="compare-the-travel-time-with-limit"></a>Vergelijk de reistijd met limiet

1. Selecteer onder de vorige actie De optie **Nieuwe stap**.

1. Selecteer **Ingebouwde**actie onder **Een actie kiezen**. Voer in het zoekvak 'voorwaarde' in als filter. Selecteer in de lijst met acties de actie **Voorwaarde.**

   ![De actie 'Voorwaarde' selecteren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Wijzig de naam van de voorwaarde met deze beschrijving: `If travel time exceeds limit`

1. Stel een voorwaarde die controleert of de waarde van de **eigenschap travelTime** de opgegeven limiet overschrijdt zoals beschreven en hier wordt weergegeven:

   1. Klik in de voorwaarde in het vak **Een waarde kiezen** aan de linkerkant van de voorwaarde.

   1. Selecteer de eigenschap **travelTime** in de lijst met dynamische inhoud die wordt weergegeven onder **Variabelen.**

      ![Bouw de linkerkant van de voorwaarde](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Selecteer in het middelste vergelijkingsvak de **optie groter is dan** de operator.

   1. Voer in het vak **Een waarde kiezen** aan de rechterkant van de voorwaarde de als volgt uit:`15`

      Wanneer u klaar bent, ziet de voorwaarde er als volgt uit:

      ![Voltooide voorwaarde om de reistijd te controleren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Sla uw logische app op.

Voeg vervolgens de actie toe die moet worden uitgevoerd wanneer de reistijd uw limiet overschrijdt.

## <a name="send-email-when-limit-exceeded"></a>E-mail verzenden als de limiet wordt overschreden

Voeg nu de actie toe die ervoor zorgt dat u een e-mailbericht ontvangt als de reistijd uw limiet overschrijdt. Dit e-mailbericht bevat de actuele reistijd en de extra tijd die nodig is om de opgegeven route af te leggen.

1. Selecteer Een **actie toevoegen**in de optie **Als waar** in de voorwaarde .

1. Selecteer Onder **Een actie kiezen**de optie **Standaard**. Voer in het zoekvak 'e-mail verzenden' in. De lijst retourneert veel resultaten, dus selecteer eerst de gewenste e-mailconnector, bijvoorbeeld:

   ![Selecteer de gewenste e-mailconnector](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Selecteer **Office 365 Outlook**voor Azure-werk- of schoolaccounts .
   * Selecteer **Outlook.com**voor persoonlijke Microsoft-accounts.

1. Wanneer de acties van de connector worden weergegeven, selecteert u 'e-mailactie verzenden' die u wilt gebruiken, bijvoorbeeld:

   ![De actie 'Een e-mail verzenden' selecteren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Als u nog geen verbinding hebt, wordt u gevraagd om u aan te melden bij uw e-mailaccount.

   Logic Apps maakt een verbinding met uw e-mailaccount.

1. Wijzig de naam van de actie met deze beschrijving: `Send email with travel time`

1. Voer het e-mailadres van de ontvanger in het vak **Aan** in. Voor testdoeleinden dient u uw e-mailadres te gebruiken.

1. Geef het onderwerp van het e-mailbericht op in het vak **Onderwerp** en sluit de variabele **travelTime** in.

   1. Voer de tekst in `Current travel time (minutes):` met een spatie aan het eind. 

   1. Selecteer in de lijst met dynamische inhoud onder **Variabelen**de optie **Meer weergeven**.

      ![Variabele 'travelTime' zoeken](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Nadat **de reistijd** wordt weergegeven onder **Variabelen**, selecteert u **travelTime**.

      ![Voer het onderwerp in plus de expressie die de reistijd retourneert](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Geef de inhoud van de hoofdtekst van de e-mail op in het vak **Hoofdtekst**.

   1. Voer de tekst in `Add extra travel time (minutes):` met een spatie aan het eind.

   1. Selecteer **Expressie**in de lijst met dynamische inhoud .

      ![Expressie maken voor hoofdtekst van e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. Voer deze expressie in in de expressie-editor, zodat u het aantal minuten waarmee de limiet wordt overschreden, kunt berekenen: ```sub(,15)```

      ![Voer de expressie in voor het berekenen van de extra reistijd in minuten](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Plaats de cursor in de expressie tussen het haakje openen (**(**) en de komma (**,**). Selecteer **Dynamische inhoud**.

      ![Ga verder met het maken van de expressie voor het berekenen van de extra reistijd in minuten](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Selecteer onder **Variabelen****travelTime**.

      ![De eigenschap 'travelTime' selecteren die u wilt gebruiken in expressie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Nadat de eigenschap in de expressie is opgelost, selecteert u **OK**.

      ![Nadat de eigenschap 'Lichaam' is opgelost, selecteert u 'OK'](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      De eigenschap **Body** verschijnt nu zoals hier wordt weergegeven:

      ![Opgelosteigenschap 'Lichaam' in expressie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Sla uw logische app op.

Test vervolgens uw logische app, die er nu bijna net zo uitziet als dit voorbeeld:

![Voltooide voorbeeldlogica-app-werkstroom](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

Als u uw logische app handmatig wilt starten, selecteert u op de werkbalkbalk van de ontwerper de optie **Uitvoeren**.

* Als de huidige reistijd onder uw limiet blijft, doet uw logische app niets anders en wacht of het volgende interval voordat u het opnieuw controleert. 

* Als de huidige reistijd uw limiet overschrijdt, krijgt u een e-mail met de huidige reistijd en het aantal minuten boven uw limiet. Hier ziet u een voorbeeld van een e-mail die uw logische app verstuurt:

![Voorbeeld verzonden e-mail met de reistijd](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Als u geen een e-mailberichten ontvangt, controleert u de map met ongewenste e-mail. Het is mogelijk dat uw filter voor ongewenste e-mail dit soort e-mails in deze map zet. Als u niet zeker weet of uw logische app correct wordt uitgevoerd, kunt u [Problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md) raadplegen.

Gefeliciteerd, u hebt nu een herhalende logische app op basis van een planning gemaakt en uitgevoerd. 

Als u andere logische apps wilt maken die de **recidieftrigger** gebruiken, controleert u deze sjablonen, die beschikbaar zijn nadat u een logische app hebt gemaakt:

* Dagelijkse herinneringen per e-mail ontvangen.
* Oudere Azure-blobs verwijderen.
* Een bericht toevoegen aan een wachtrij van Azure Storage.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de voorbeeldlogica-app niet meer nodig hebt, verwijdert u de brongroep die uw logische app en gerelateerde bronnen bevat. 

1. Ga in het Azure-hoofdmenu naar **Resourcegroepen** en selecteer de resourcegroep voor uw logische app.

1. Selecteer resourcegroep **Overzicht** > verwijderen in het menu**Resourcegroep verwijderen**. 

   !["Overzicht" > "Resourcegroep verwijderen"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Voer de naam van de brongroep in als bevestiging en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een logische app gemaakt die verkeer controleert op basis van een opgegeven planning (‘s ochtends op doordeweekse dagen) en die actie onderneemt (e-mails verzenden) wanneer de reistijd een bepaalde limiet overschrijdt. Leer nu hoe u een logische app maakt waarmee u aanmeldingen voor een mailinglijst indient ter goedkeuring. Hiervoor gebruikt u een integratie van Azure-services, Microsoft-services en andere SaaS-apps.

> [!div class="nextstepaction"]
> [Mailinglijstaanvragen beheren](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
