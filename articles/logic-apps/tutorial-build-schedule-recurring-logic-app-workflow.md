---
title: Geautomatiseerde werk stromen op basis van een planning bouwen
description: 'Zelf studie: een op een planning gebaseerde, terugkerende, geautomatiseerde werk stroom maken met behulp van Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 5d4990fd806aed75d9b5e5ddd3e9a615631d9d65
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82146513"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Zelf studie: een geautomatiseerde, op planning gebaseerde, terugkerende werk stromen maken met behulp van Azure Logic Apps

Deze zelf studie laat zien hoe u een [logische app](../logic-apps/logic-apps-overview.md) bouwt en een terugkerende werk stroom automatiseert die volgens een planning wordt uitgevoerd. Met name deze logische app wordt elke weekdag morgen uitgevoerd en controleert de reis tijd, inclusief verkeer, tussen twee locaties. Als de tijd een bepaalde limiet overschrijdt, verzendt de logische app een e-mail met de reistijd en de extra tijd die u nodig hebt om uw bestemming te bereiken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een lege, logische app maken.
> * Voeg een terugkeer patroon trigger toe die het schema voor uw logische app specificeert.
> * Een Bing Maps-actie toevoegen die de reis tijd voor een route ophaalt.
> * Een actie toevoegen waarmee een variabele wordt gemaakt, de reis tijd wordt geconverteerd van seconden naar minuten, en het resultaat wordt opgeslagen in de variabele.
> * Een voorwaarde toevoegen die de reistijd vergelijkt met een opgegeven limiet.
> * Voeg een actie toe waarmee u e-mail berichten verzendt als de reis tijd de limiet overschrijdt.

Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Overzicht van de werk stroom voor logische apps op hoog niveau](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u [zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

* Een e-mail account van een e-mail provider die wordt ondersteund door Logic Apps, zoals Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](https://docs.microsoft.com/connectors/). In deze Snelstartgids wordt een Office 365 Outlook-account gebruikt. Als u een ander e-mail account gebruikt, blijven de algemene stappen hetzelfde, maar uw gebruikers interface kan enigszins verschillen.

  > [!IMPORTANT]
  > Als u de Gmail-connector wilt gebruiken, kunnen alleen zakelijke accounts van G-Suite deze connector gebruiken zonder beperkingen in Logic apps. Als u een Gmail-Consumer-account hebt, kunt u deze connector gebruiken met alleen specifieke door Google goedgekeurde Services, of u kunt [een Google-client-app maken die voor verificatie met uw Gmail-connector wordt gebruikt](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Zie voor meer informatie [beleid voor gegevens beveiliging en privacybeleid voor Google connectors in azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Als u de reistijd voor een route wilt ophalen, hebt u een toegangssleutel nodig voor de API van Bing Kaarten. Volg de stappen in [Een Bing Kaarten-sleutel krijgen](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) om deze sleutel te krijgen.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Selecteer in het hoofd menu van Azure **een resource** > **Integration** > **Logic-app**maken.

   ![Uw logische app-resource maken](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. In het menu **Logische app maken** geeft u de informatie over uw logische app op zoals hier wordt weergegeven en beschreven. Als u gereed bent, selecteert u **Maken**.

   ![Informatie over uw logische app opgeven](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | LA-TravelTime | De naam van uw logische app, die alleen letters, cijfers, afbreek streepjes (`-`), onderstrepings tekens (`_`), haakjes (`(`, `)`) en punten (`.`) kan bevatten. In dit voor beeld wordt gebruikgemaakt van ' LA-TravelTime '. |
   | **Abonnement** | <*de naam van uw Azure-abonnement*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | LA-TravelTime-RG | De naam voor de [Azure-resource groep](../azure-resource-manager/management/overview.md), die wordt gebruikt om verwante resources te organiseren. In dit voor beeld wordt ' LA-TravelTime-RG ' gebruikt. |
   | **Locatie** | VS - west | De tKan-regio waar de logische app-gegevens moeten worden opgeslagen. In dit voor beeld wordt ' West US ' gebruikt. |
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u in de Azure-werk balk de optie **meldingen** > **gaat u** naar de resource voor uw geïmplementeerde logische app.

   ![Ga naar de nieuwe logische app-resource](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   U kunt ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps Designer wordt geopend en toont een pagina met een introductie video en veelgebruikte triggers en logica patronen voor logische apps. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Sjabloon voor lege logische app selecteren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Voeg vervolgens de terugkeer patroon [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts)toe, die wordt geactiveerd op basis van een opgegeven schema. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer nieuwe gegevens voldoen aan een bepaalde voorwaarde. Bekijk [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie.

## <a name="add-the-recurrence-trigger"></a>De trigger voor terugkeer patroon toevoegen

1. Voer in de ontwerp functie voor logische apps in het zoekvak ' recurrence ' in als uw filter. Selecteer in de lijst **Triggers** de trigger **terugkeer patroon** .

   ![De trigger ' recurrence ' toevoegen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Selecteer op de vorm van het **terugkeer patroon** de knop met **weglatings** tekens (**...**) en selecteer vervolgens **naam wijzigen**. Wijzig de naam van de trigger met deze beschrijving: `Check travel time every weekday morning`

   ![De beschrijving van de terugkeer patroon naam wijzigen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Wijzig de eigenschappen in de trigger.

   ![Het interval en de frequentie van de terugkeer trigger wijzigen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Bereik** | Ja | 1 | Het aantal intervallen dat tussen controles moet worden gewacht |
   | **Frequentie** | Ja | Wekelijks | Tijdseenheid die voor het terugkeerpatroon wordt gebruikt |
   |||||

1. Open onder **interval** en **frequentie**de lijst **nieuwe para meter toevoegen** en selecteer deze eigenschappen om aan de trigger toe te voegen.

   * **Deze dagen**
   * **Deze uren**
   * **Deze minuten**

   ![Eigenschappen voor de trigger voor terugkeer patroon toevoegen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Stel nu de waarden voor de extra eigenschappen in zoals hier wordt weer gegeven en beschreven.

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

1. Sla uw logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

Uw logische app is nu live, maar doet niets anders dan herhalen. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="get-the-travel-time-for-a-route"></a>De reistijd voor een route ophalen

Nu u een trigger hebt, voegt u een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die de reistijd tussen twee locaties ophaalt. Logic Apps biedt een connector voor de API van Bing Kaarten. Hiermee kunt u deze informatie eenvoudig ophalen. Voordat u deze taak start, moet u ervoor zorgen dat u een API-sleutel van Bing Kaarten hebt, zoals beschreven in de vereisten in deze zelfstudie.

1. Selecteer in de Logic app Designer onder uw trigger de optie **nieuwe stap**.

1. Selecteer onder **Kies een actie de**optie **standaard**. In het zoekvak voert u ' Bing Maps ' in als uw filter en selecteert u de actie **route ophalen** .

   ![Selecteer de actie route ophalen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Als u geen verbinding met Bing Kaarten hebt, wordt u gevraagd om een verbinding te maken. Geef deze verbindings gegevens op en selecteer **maken**.

   ![Verbinding maken met de Bing Maps-API](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Verbindings naam** | Ja | BingMapsConnection | Geef een naam op voor uw verbinding. In dit voor beeld wordt ' BingMapsConnection ' gebruikt. |
   | **API-sleutel** | Ja | <*uw-Bing-Kaarten-sleutel*> | Voer de sleutel voor Bing Kaarten in die u eerder hebt ontvangen. Leer [hoe u een sleutel kunt verkrijgen](https://msdn.microsoft.com/library/ff428642.aspx) als u geen sleutel voor Bing Kaarten hebt. |
   |||||

1. Wijzig de naam van de actie met deze beschrijving: `Get route and travel time with traffic`

1. Open in de actie de **lijst nieuwe para meter toevoegen**en selecteer deze eigenschappen om aan de actie toe te voegen.

   * **Optimaliseren**
   * **Afstandseenheid**
   * **Vervoermiddel**

   ![Eigenschappen toevoegen aan de actie route ophalen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Stel nu de waarden in voor de eigenschappen van de actie, zoals hier wordt weer gegeven en beschreven.

   ![Geef details op voor de actie route ophalen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Routepunt 1** | Ja | <*Start-locatie*> | Het beginpunt van uw route |
   | **Routepunt 2** | Ja | <*eind locatie*> | De bestemming van uw route |
   | **Optimaliseren** | Nee | timeWithTraffic | Een parameter voor het optimaliseren van uw route, zoals afstand, reistijd in actuele verkeerssituatie, enzovoort. Selecteer de para meter ' timeWithTraffic '. |
   | **Afstandseenheid** | Nee | <*uw voor keur*> | De afstandseenheid die voor de route wordt gebruikt. In dit voor beeld wordt ' mijl ' gebruikt als eenheid. |
   | **Vervoermiddel** | Nee | Auto | Het vervoermiddel voor uw route. Selecteer de modus aangedreven. |
   ||||

   Zie [Een route berekenen](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route) voor meer informatie over deze parameters.

1. Sla uw logische app op.

Maak vervolgens een variabele, zodat u de actuele reistijd kunt converteren en opslaan in minuten in plaats van seconden. Zo kunt u voorkomen dat de conversie wordt herhaald en kunt u de waarden eenvoudiger gebruiken in latere stappen. 

## <a name="create-a-variable-to-store-travel-time"></a>Een variabele maken om de reis tijd op te slaan

Soms wilt u mogelijk bewerkingen uitvoeren op gegevens in uw werk stroom en vervolgens de resultaten in latere acties gebruiken. U kunt variabelen maken die deze resultaten opslaan nadat ze die hebben verwerkt. Zo kunt u de opgeslagen resultaten later gemakkelijk hergebruiken of raadplegen. U kunt alleen variabelen maken in het hoogste niveau van uw logische app.

De vorige actie **route ophalen** retourneert standaard de huidige reis tijd met verkeer in seconden van de eigenschap **reis duur verkeer** . Door deze waarde in plaats daarvan te converteren en op te slaan, kunt u de waarde later eenvoudiger hergebruiken zonder dat u opnieuw moet omrekenen.

1. Selecteer **nieuwe stap**onder de actie **route ophalen** .

1. Selecteer onder **Kies een actie de**optie **ingebouwd**. Voer in het zoekvak ' variabelen ' in en selecteer de actie **variabele initialiseren** .

   ![Selecteer de actie variabele initialiseren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Wijzig de naam van deze actie met deze beschrijving: `Create variable to store travel time`

1. Geef details op voor uw variabele zoals hier wordt beschreven:

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Naam** | Ja | travelTime | De naam van de variabele. In dit voor beeld wordt ' travelTime ' gebruikt. |
   | **Type** | Ja | Geheel getal | Het gegevenstype van uw variabele |
   | **Waarde** | Nee| Een expressie die de actuele reistijd van seconden naar minuten converteert (zie de stappen onder deze tabel). | De beginwaarde van uw variabele |
   ||||

   1. Als u de expressie voor de eigenschap **Value** wilt maken, klikt u in het vak zodat de lijst met dynamische inhoud wordt weer gegeven. Als u de lijst niet ziet, verbreedt u uw browser totdat de lijst wel wordt weergegeven. Selecteer **expressie**in de lijst met dynamische inhoud.

      ![Informatie opgeven voor de actie variabele initialiseren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Wanneer u in sommige invoer vakken klikt, wordt de lijst met dynamische inhoud weer gegeven. In deze lijst worden de eigenschappen van vorige acties weer gegeven die u als invoer in uw werk stroom kunt gebruiken. De lijst met dynamische inhoud heeft een expressie-editor waarmee u functies kunt selecteren om bewerkingen uit te voeren. Deze expressie-editor wordt alleen weergegeven in de lijst met dynamische inhoud.

   1. Voer deze expressie in in de expressie-editor: `div(,60)`

      ![Voer deze expressie in "div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Plaats de cursor in de expressie tussen het haakje openen (**(**) en de komma (**,**). 
   Selecteer **dynamische inhoud**.

      ![Positie cursor, selecteer ' dynamische inhoud '](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Selecteer **Reistijd in huidige verkeerssituatie** in de lijst met dynamische inhoud.

      ![De eigenschap ' reis duur verkeer ' selecteren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Nadat de eigenschaps waarde in de expressie is omgezet, selecteert u **OK**.

      ![Selecteer OK om de expressie te maken.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      De eigenschap **Value** verschijnt nu zoals hier wordt weer gegeven:

      ![De eigenschap Value wordt weer gegeven met de omgezette expressie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Sla uw logische app op.

Vervolgens voegt u een voorwaarde toe waarmee wordt gecontroleerd of de actuele reistijd een bepaalde limiet overschrijdt.

## <a name="compare-the-travel-time-with-limit"></a>De reis tijd vergelijken met de limiet

1. Selecteer **nieuwe stap**onder de vorige actie.

1. Selecteer onder **Kies een actie de**optie **ingebouwd**. Voer in het zoekvak ' condition ' in als uw filter. Selecteer in de lijst acties de actie **voor de voor waarde** .

   ![Selecteer de actie voor waarde](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Wijzig de naam van de voorwaarde met deze beschrijving: `If travel time exceeds limit`

1. Maak een voor waarde waarmee wordt gecontroleerd of de waarde van de eigenschap **travelTime** groter is dan de opgegeven limiet, zoals hier wordt beschreven en weer gegeven:

   1. In de voor waarde klikt u in het vak **een waarde kiezen** op de linkerkant van de voor waarde.

   1. Selecteer in de lijst met dynamische inhoud die wordt weer gegeven onder **variabelen**de eigenschap **travelTime** .

      ![De linkerkant van de voor waarde maken](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Selecteer in het vak middelste vergelijking de operator **is groter dan** .

   1. Geef in het vak **een waarde kiezen** op de rechter zijde de volgende limiet op:`15`

      Wanneer u klaar bent, ziet de voor waarde eruit als in dit voor beeld:

      ![Voor waarde voor het controleren van de reis tijd is voltooid](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Sla uw logische app op.

Voeg vervolgens de actie toe die moet worden uitgevoerd wanneer de reis tijd de limiet overschrijdt.

## <a name="send-email-when-limit-exceeded"></a>E-mail verzenden als de limiet wordt overschreden

Voeg nu de actie toe die ervoor zorgt dat u een e-mailbericht ontvangt als de reistijd uw limiet overschrijdt. Dit e-mailbericht bevat de actuele reistijd en de extra tijd die nodig is om de opgegeven route af te leggen.

1. Selecteer **een actie toevoegen**in de vertakking **Indien waar** van de voor waarde.

1. Selecteer onder **Kies een actie de**optie **standaard**. Voer in het zoekvak e-mail verzenden in. De lijst retourneert veel resultaten. Selecteer daarom eerst de gewenste e-mail Connector, bijvoorbeeld:

   ![Selecteer de gewenste e-mail Connector](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Selecteer **Office 365 Outlook** voor werk- of schoolaccounts van Azure.
   * Selecteer **Outlook.com** voor persoonlijke Microsoft-accounts.

1. Wanneer de acties van de connector worden weer gegeven, selecteert u ' e-mail actie verzenden ' die u wilt gebruiken, bijvoorbeeld:

   ![De actie 'Een e-mail verzenden' selecteren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Als u nog geen verbinding hebt, wordt u gevraagd om u aan te melden bij uw e-mailaccount.

   Logic Apps maakt een verbinding met uw e-mailaccount.

1. Wijzig de naam van de actie met deze beschrijving: `Send email with travel time`

1. Voer het e-mailadres van de ontvanger in het vak **Aan** in. Voor testdoeleinden dient u uw e-mailadres te gebruiken.

1. Geef het onderwerp van het e-mailbericht op in het vak **Onderwerp** en sluit de variabele **travelTime** in.

   1. Voer de tekst in `Current travel time (minutes):` met een spatie aan het eind. 

   1. Selecteer in de lijst met dynamische inhoud onder **variabelen**de optie **meer weer geven**.

      ![De variabele ' travelTime ' zoeken](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Nadat **travelTime** onder **Varia bles**wordt weer gegeven, selecteert u **travelTime**.

      ![Voer het onderwerp in plus de expressie die de reistijd retourneert](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Geef de inhoud van de hoofdtekst van de e-mail op in het vak **Hoofdtekst**.

   1. Voer de tekst in `Add extra travel time (minutes):` met een spatie aan het eind.

   1. Selecteer **expressie**in de lijst met dynamische inhoud.

      ![Expressie maken voor hoofdtekst van e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. Voer deze expressie in in de expressie-editor, zodat u het aantal minuten waarmee de limiet wordt overschreden, kunt berekenen: ```sub(,15)```

      ![Voer de expressie in voor het berekenen van de extra reistijd in minuten](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Plaats de cursor in de expressie tussen het haakje openen (**(**) en de komma (**,**). Selecteer **dynamische inhoud**.

      ![Ga verder met het maken van de expressie voor het berekenen van de extra reistijd in minuten](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Selecteer onder **Variabelen****travelTime**.

      ![Selecteer de eigenschap travelTime voor gebruik in de expressie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Nadat de eigenschap in de expressie is opgelost, selecteert u **OK**.

      ![Nadat de eigenschap Body is opgelost, selecteert u OK](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      De eigenschap **Body** wordt nu weer gegeven zoals hier wordt weer gegeven:

      ![Opgeloste eigenschap Body in expressie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Sla uw logische app op.

Test vervolgens uw logische app, die er nu bijna net zo uitziet als dit voorbeeld:

![Voltooide voorbeeld werk stroom voor logische apps](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

Als u de logische app hand matig wilt starten, selecteert u **uitvoeren**op de werk balk van de ontwerp functie.

* Als de huidige reis tijd onder uw limiet blijft, doet uw logische app niets anders en wacht u of het volgende interval voordat u opnieuw controleert. 

* Als de huidige reis tijd de limiet overschrijdt, ontvangt u een e-mail bericht met de huidige reis tijd en het aantal minuten boven uw limiet. Hier ziet u een voorbeeld van een e-mail die uw logische app verstuurt:

![Voor beeld van een e-mail bericht waarin de reis tijd wordt weer gegeven](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Als u geen een e-mailberichten ontvangt, controleert u de map met ongewenste e-mail. Het is mogelijk dat uw filter voor ongewenste e-mail dit soort e-mails in deze map zet. Als u niet zeker weet of uw logische app correct wordt uitgevoerd, kunt u [Problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md) raadplegen.

Gefeliciteerd, u hebt nu een herhalende logische app op basis van een planning gemaakt en uitgevoerd. 

Als u andere logische Apps wilt maken die gebruikmaken van de **terugkeer patroon** trigger, bekijkt u deze sjablonen, die beschikbaar zijn nadat u een logische app hebt gemaakt:

* Dagelijkse herinneringen per e-mail ontvangen.
* Oudere Azure-blobs verwijderen.
* Een bericht toevoegen aan een wachtrij van Azure Storage.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de voor beeld-logische app niet meer nodig hebt, verwijdert u de resource groep die uw logische app en gerelateerde resources bevat. 

1. Ga in het Azure-hoofdmenu naar **Resourcegroepen** en selecteer de resourcegroep voor uw logische app.

1. Selecteer **overzicht** > **resource groep verwijderen**in het menu resource groep. 

   !["Overzicht" > "Resourcegroep verwijderen"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Voer de naam van de resource groep in als bevestiging en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een logische app gemaakt die verkeer controleert op basis van een opgegeven planning (‘s ochtends op doordeweekse dagen) en die actie onderneemt (e-mails verzenden) wanneer de reistijd een bepaalde limiet overschrijdt. Leer nu hoe u een logische app maakt waarmee u aanmeldingen voor een mailinglijst indient ter goedkeuring. Hiervoor gebruikt u een integratie van Azure-services, Microsoft-services en andere SaaS-apps.

> [!div class="nextstepaction"]
> [Mailinglijstaanvragen beheren](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
