---
title: Geautomatiseerde werk stromen op basis van goed keuring bouwen
description: 'Zelf studie: een geautomatiseerde werk stroom op basis van goed keuring maken waarmee abonnementen van adressen lijsten worden verwerkt met behulp van Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: 7d7f573e5b18e6e0e63d3275aecefe408a9143fb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456616"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Zelf studie: geautomatiseerde werk stromen op basis van goed keuring maken met behulp van Azure Logic Apps

Deze zelf studie laat zien hoe u een [logische app](../logic-apps/logic-apps-overview.md) bouwt die een werk stroom op basis van goed keuring automatiseert. Met name worden met deze logische app abonnements aanvragen verwerkt voor een adressen lijst die wordt beheerd door de [MailChimp](https://mailchimp.com/) -service. Deze logische app bewaakt een e-mailaccount voor deze aanvragen, verzendt deze aanvragen voor goedkeuring en voegt goedgekeurde leden toe aan de adressenlijst.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een lege, logische app maken.
> * Een trigger toevoegen waarmee e-mailberichten op abonnementaanvragen worden gecontroleerd.
> * Een actie toevoegen waarmee e-mailberichten worden verzonden om deze aanvragen goed te keuren of af te wijzen.
> * Een voorwaarde toevoegen voor het controleren van het goedkeuringsantwoord.
> * Een actie toevoegen waarmee goedgekeurde leden worden toegevoegd aan de adressenlijst.
> * Een voorwaarde toevoegen waarmee wordt gecontroleerd of deze leden met succes aan de lijst zijn toegevoegd.
> * Een voorwaarde toevoegen waarmee e-mailberichten worden verzonden om te bevestigen of deze leden met succes aan de lijst zijn toegevoegd.

Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Overzicht van voltooide logische apps op hoog niveau](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u [zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

* Een MailChimp-account dat een lijst bevat met de naam ' test-leden-ML ', waarbij uw logische app e-mail adressen voor goedgekeurde leden kan toevoegen. Als u geen account hebt, [meldt u zich aan voor een gratis account](https://login.mailchimp.com/signup/)en leert [u hoe u een MailChimp-lijst maakt](https://us17.admin.mailchimp.com/lists/#).

* Een e-mail account in Office 365 Outlook of Outlook.com, dat goedkeurings werk stromen ondersteunt. In dit artikel wordt gebruikgemaakt van Outlook van Office 365. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Selecteer in het hoofd menu van Azure **een resource maken** > **integratie** > **logische app**.

   ![De nieuwe logische app-resource maken](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. In het menu **Logische app maken** geeft u de informatie over uw logische app op zoals hier wordt weergegeven en beschreven. Als u gereed bent, selecteert u **Maken**.

   ![Informatie over uw logische app opgeven](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | LA-MailingList | De naam van uw logische app, die alleen letters, cijfers, afbreek streepjes (`-`), onderstrepings tekens (`_`), haakjes (`(`, `)`) en punten (`.`) kan bevatten. In dit voor beeld wordt gebruikgemaakt van ' LA-MailingList '. |
   | **Abonnement** | <*your-Azure-subscription-name*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | LA-MailingList-RG | De naam voor de [Azure-resource groep](../azure-resource-manager/management/overview.md), die wordt gebruikt om verwante resources te organiseren. In dit voor beeld wordt ' LA-MailingList-RG ' gebruikt. |
   | **Locatie** | VS - west | De tKan-regio waar de logische app-gegevens moeten worden opgeslagen. In dit voor beeld wordt ' West US ' gebruikt. |
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u op de Azure-werk balk **meldingen** > **gaat u naar de resource** voor uw geïmplementeerde logische app.

   ![Ga naar de nieuwe logische app-resource](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   U kunt ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps Designer wordt geopend en toont een pagina met een introductie video en veelgebruikte triggers en logica patronen voor logische apps. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Sjabloon voor lege logische app selecteren](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Voeg vervolgens een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die luistert naar binnenkomende e-mailberichten met abonnementaanvragen. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer een bepaalde gebeurtenis plaatsvindt of wanneer nieuwe gegevens aan een specifieke voor waarde voldoen. Bekijk [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie.

## <a name="add-trigger-to-monitor-emails"></a>Trigger voor het bewaken van e-mailberichten toevoegen

1. Voer in de ontwerp functie voor logische apps in het zoekvak `when email arrives` in als uw filter. Selecteer in de lijst **Triggers** de trigger **wanneer er een nieuwe e-mail binnenkomt** voor uw e-mail provider.

   In dit voor beeld wordt de Office 365 Outlook-trigger gebruikt:

   ![Selecteer de trigger ' wanneer een nieuwe e-mail binnenkomt ' voor de e-mail provider](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Voor werk- of schoolaccounts van Azure selecteert u Outlook van Office 365.
   * Selecteer Outlook.com voor persoonlijke Microsoft-accounts.

1. Als u hierom wordt gevraagd, meldt u zich aan bij uw e-mail account met uw referenties zodat Logic Apps een verbinding met uw e-mail account kunt maken.

1. Geef in de trigger de criteria op voor het controleren van alle nieuwe e-mail.

   1. Geef de map, interval en frequentie voor het controleren van e-mails op.

      ![Geef map, interval en frequentie voor het controleren van e-mails op](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Eigenschap | Waarde | Beschrijving |
      |----------|-------|-------------|
      | **Map** | `Inbox` | De te bewaken e-mailmap |
      | **Interval** | `1` | Het aantal intervallen dat tussen controles moet worden gewacht |
      | **Frequentie** | `Hour` | Tijdseenheid die voor het terugkeerpatroon wordt gebruikt |
      ||||

   1. Voeg nu een andere eigenschap toe aan de trigger zodat u kunt filteren op de onderwerpregel van het e-mail bericht. Open de **lijst nieuwe para meter toevoegen**en selecteer de eigenschap **onderwerps filter** .

      ![Eigenschap Subject filter toevoegen aan trigger](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Meer informatie over de eigenschappen van deze trigger vindt u in de naslag gids voor [Office 365 Outlook-Connector](https://docs.microsoft.com/connectors/office365/) of de [Outlook.com-connector](https://docs.microsoft.com/connectors/outlook/).

   1. Nadat de eigenschap in de trigger wordt weer gegeven, voert u deze tekst in: `subscribe-test-members-ML`

      ![Voer tekst in voor de eigenschap "subject filter"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Als u de details van de trigger voorlopig wilt verbergen, klikt u op de titelbalk van de trigger.

   ![Shape samenvouwen om details te verbergen](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

Uw logische app is nu live, maar doet niets behalve uw binnenkomende e-mail controleren. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="send-approval-email"></a>Goedkeurings-e-mail verzenden

Nu u een trigger hebt, kunt u een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toevoegen waarmee een e-mailbericht wordt verzonden om de aanvraag goed te keuren of af te wijzen.

1. Selecteer **nieuwe stap**onder de trigger. 

1. Voer in het zoekvak onder **Kies een actie**`approval` in als uw filter. Selecteer in de lijst acties de actie **e-mail voor goed keuring verzenden** voor uw e-mail provider. 

   In dit voor beeld wordt de Office 365 Outlook-actie gebruikt:

   ![Selecteer de actie goed keuring e-mail verzenden](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Geef de informatie over deze actie op zoals beschreven: 

   ![E-mail eigenschappen voor goed keuring verzenden](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Aan** | <*uw-e-mailadres*> | Het e-mailadres van de fiatteur. Voor testdoeleinden kunt u uw eigen adres gebruiken. In dit voor beeld wordt het fictieve e-mail adres 'sophia.owen@fabrikam.com' gebruikt. |
   | **Onderwerp** | `Approve member request for test-members-ML` | Een beschrijvend e-mailonderwerp |
   | **Gebruikersopties** | `Approve, Reject` | De antwoord opties die de goed keurder kan selecteren. Standaard kan de goed keurder ofwel ' goed keuren ' of ' afwijzen ' selecteren als antwoord. |
   ||||

   U kunt nu de lijst met dynamische inhoud negeren die wordt weer gegeven wanneer u in bepaalde bewerkings vakken klikt. Met deze lijst kunt u beschik bare uitvoer van vorige acties selecteren die u als invoer in uw werk stroom kunt gebruiken.

   Voor meer informatie over de eigenschappen van deze actie, zie de naslag informatie voor [Office 365 Outlook Connector](https://docs.microsoft.com/connectors/office365/) of de [referentie voor Outlook.com-connector](https://docs.microsoft.com/connectors/outlook/).
 
1. Sla uw logische app op.

Voeg vervolgens een voor waarde toe om het geselecteerde antwoord van de fiatteur te controleren.

## <a name="check-approval-response"></a>Goedkeuringsantwoord controleren

1. Selecteer **nieuwe stap**onder de actie **goed keuring e-mail verzenden** .

1. Selecteer onder **Kies een actie de**optie **ingebouwd**. Voer in het zoekvak `condition` in als uw filter. Selecteer in de lijst acties de actie **voor de voor waarde** .

   ![Zoek en selecteer de actie voor waarde](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Geef de voorwaarde een naam met een betere beschrijving.

   1. Selecteer op de titel balk van de voor waarde de knop met **weglatings** tekens ( **...** ) > **naam wijzigen**.

      ![Beschrijving van voor waarde naam wijzigen](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Verander de naam van uw voorwaarde in deze beschrijving: `If request approved`

1. Maak een voor waarde waarmee wordt gecontroleerd of de fiatteur **goed keuring**heeft geselecteerd.

   1. In de voor waarde klikt u in het vak **een waarde kiezen** op de linkerkant van de voor waarde.

   1. Selecteer de eigenschap **token** in de lijst met dynamische inhoud die wordt weer gegeven onder **goedkeurings-e-mail verzenden**.

      ![Selecteer ' token ' in de lijst met dynamische inhoud.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Selecteer in het vak middelste vergelijking de operator **is gelijk aan** .

   1. Voer in het vak **een waarde kiezen** op de rechter kant van de voor waarde de volgende tekst in: `Approve`

      Wanneer u klaar bent, ziet de voor waarde eruit als in dit voor beeld:

      ![Voor waarde voltooid voor goedgekeurd voor beeld](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Sla uw logische app op.

Geef vervolgens de actie op die uw logische app uitvoert wanneer de fiatteur de aanvraag goedkeurt. 

## <a name="add-member-to-mailchimp-list"></a>Lid toevoegen aan MailChimp-lijst

Voeg nu een actie toe waarmee het goedgekeurde lid wordt toegevoegd aan uw adressen lijst.

1. Selecteer **een actie toevoegen**in de vertakking **Indien waar** van de voor waarde.

1. Onder **Kies een actie**, voert u `mailchimp` in als uw filter en selecteert **u de actie lid toevoegen aan lijst** .

   ![Selecteer de actie ' leden toevoegen aan lijst '](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Als u wordt gevraagd om toegang te krijgen tot uw MailChimp-account, meldt u zich aan met uw MailChimp-referenties.

1. Geef informatie op over deze actie zoals hier wordt weer gegeven en beschreven:

   ![Informatie opgeven voor 'Add member to list'](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Eigenschap | Verplicht | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **List Id (Lijst-id)** | Ja | `test-members-ML` | De naam voor uw MailChimp-adressen lijst. In dit voor beeld wordt gebruikgemaakt van ' test-members-ML '. |
   | **Status** | Ja | `subscribed` | Selecteer de abonnements status voor het nieuwe lid. In dit voor beeld wordt gebruikgemaakt van ' geabonneerd '. <p>Zie voor meer informatie [Manage subscribers with the MailChimp API (Abonnees beheren met de MailChimp-API)](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **Email Address (E-mailadres)** | Ja | <*e-mailadres-nieuw-lid*> | Selecteer in de lijst met dynamische inhoud **van** onder **wanneer er een nieuwe e-mail binnenkomt**, die het e-mail adres voor het nieuwe lid doorgeeft. |
   ||||

   Meer informatie over de eigenschappen van deze actie vindt u in de [MailChimp-connector verwijzing](https://docs.microsoft.com/connectors/mailchimp/).

1. Sla uw logische app op.

Voeg vervolgens een voorwaarde toe waarmee u kunt controleren of het nieuwe lid met succes aan uw adressenlijst is toegevoegd. Op die manier meldt uw logische app u of de bewerking is geslaagd of mislukt.

## <a name="check-for-success-or-failure"></a>Controleren op slagen of mislukken

1. In de vertakking **Indien waar** , onder de actie **lid toevoegen aan lijst** , selecteert u **een actie toevoegen**.

1. Selecteer onder **Kies een actie de**optie **ingebouwd**. Voer in het zoekvak `condition` in als uw filter. Selecteer in de lijst acties de optie **voor waarde**.

1. Wijzig de naam van de voorwaarde met deze beschrijving: `If add member succeeded`

1. Maak een voorwaarde die controleert of het goedgekeurde lid met succes aan uw adressenlijst is toegevoegd of niet:

   1. In de voor waarde klikt u in het vak **een waarde kiezen** , die aan de linkerkant van de voor waarde wordt weer gegeven. Selecteer de eigenschap **status** in de lijst met dynamische inhoud onder **lid toevoegen aan lijst**.

      Uw voor waarde ziet er bijvoorbeeld uit als in dit voor beeld:

      ![Onder ‘Lid toevoegen aan lijst’ selecteert u ‘Status’](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Selecteer in het vak middelste vergelijking de operator **is gelijk aan** .

   1. Voer in het vak **een waarde kiezen** op de rechter kant van de voor waarde de volgende tekst in: `subscribed`

      Wanneer u klaar bent, ziet de voor waarde eruit als in dit voor beeld:

      ![Voor waarde voor geabonneerd voor beeld voltooid](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Stel vervolgens de e-mailberichten in die moeten worden verzonden als het goedgekeurde lid met succes aan uw adressenlijst is toegevoegd of niet.

## <a name="send-email-if-member-added"></a>E-mailbericht verzenden als lid is toegevoegd

1. Selecteer **een actie toevoegen**onder de voor waarde **als lid van toe te voegen toevoegen** in de vertakking **Indien waar** .

   ![Selecteer een actie toevoegen in de vertakking indien waar.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Voer onder **Kies een actie**in het zoekvak `outlook send email` in als uw filter en selecteer de actie **een e-mail verzenden** .

   ![De actie een e-mail verzenden toevoegen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Wijzig de naam van de actie met deze beschrijving: `Send email on success`

1. Geef informatie voor deze actie op zoals weergegeven en beschreven:

   ![Informatie opgeven voor e-mail bij succes](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Eigenschap | Verplicht | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Aan** | Ja | <*uw-e-mailadres*> | Het e-mailadres waarnaar het bericht bij succes moet worden verzonden. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   | **Onderwerp** | Ja | <*onderwerp-voor-succes-e-mail*> | Het onderwerp voor het e-mailbericht bij succes. Voer voor deze zelfstudie deze tekst in: <p>`Success! Member added to "test-members-ML": ` <p>Selecteer in de lijst dynamische inhoud onder **lid toevoegen aan lijst**de eigenschap **e-mail adres** . |
   | **Hoofdtekst** | Ja | <*hoofdtekst-voor-succes-e-mail*> | De hoofdtekst voor het e-mailbericht bij succes. Voer voor deze zelfstudie deze tekst in: <p>`New member has joined "test-members-ML":` <p>Selecteer in de lijst met dynamische inhoud de eigenschap **e-mail adres** . <p>Voer deze tekst in op de volgende rij: `Member opt-in status: ` <p> Selecteer de eigenschap **status** in de lijst met dynamische inhoud onder **lid toevoegen aan lijst**. |
   |||||

1. Sla uw logische app op.

## <a name="send-email-if-member-not-added"></a>E-mailbericht verzenden als lid niet is toegevoegd

1. Selecteer **een actie toevoegen**onder de voor waarde **Indien als lid is geslaagd** in de vertakking **indien onwaar** .

   ![Selecteer in ' indien onwaar ' vertakking een actie toevoegen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Voer onder **Kies een actie**in het zoekvak `outlook send email` in als uw filter en selecteer de actie **een e-mail verzenden** .

   ![Actie voor ‘Een e-mail verzenden’ toevoegen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Wijzig de naam van de actie met deze beschrijving: `Send email on failure`

1. Geef informatie op over deze actie zoals hier wordt weer gegeven en beschreven:

   ![Informatie opgeven voor e-mail bij mislukken](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Eigenschap | Verplicht | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Aan** | Ja | <*uw-e-mailadres*> | Het e-mailadres waarnaar het bericht bij mislukken moet worden verzonden. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   | **Onderwerp** | Ja | <*onderwerp-voor-e-mail-bij-mislukken*> | Het onderwerp voor het e-mailbericht bij mislukken. Voer voor deze zelfstudie deze tekst in: <p>`Failed, member not added to "test-members-ML": ` <p>Selecteer in de lijst dynamische inhoud onder **lid toevoegen aan lijst**de eigenschap **e-mail adres** . |
   | **Hoofdtekst** | Ja | <*hoofdtekst-voor-e-mail-bij-mislukken*> | De hoofdtekst voor het e-mailbericht bij mislukken. Voer voor deze zelfstudie deze tekst in: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Sla uw logische app op. 

Test vervolgens uw logische app, die er nu bijna net zo uitziet als dit voorbeeld:

![Voor beeld van een voltooide werk stroom voor logische apps](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

1. Stuur uzelf een e-mailbericht met een aanvraag om lid te worden van uw adressenlijst. Wacht totdat de aanvraag in uw Postvak IN aankomt.

1. Als u de logische app hand matig wilt starten, selecteert u **uitvoeren**op de werk balk van de ontwerp functie. 

   Als uw e-mail een onderwerp heeft dat overeenkomt met het onderwerpfilter van de trigger, stuurt uw logische app u een e-mail om de abonnementaanvraag goed te keuren.

1. Selecteer **goed**keuren in de goedkeurings-e-mail.

1. Als het e-mailadres van de abonnee niet voorkomt in uw adressenlijst, voegt uw logische app het e-mailadres van die persoon toe en stuurt u een e-mail zoals dit voorbeeld:

   ![Voor beeld van e-mail berichten-geslaagde abonnementen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Als uw logische app de abonnee niet kan toevoegen, krijgt u een e-mail als in dit voorbeeld:

   ![Voor beeld van e-mail-abonnement mislukt](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Als u geen een e-mailberichten ontvangt, controleert u de map met ongewenste e-mail. Het is mogelijk dat uw filter voor ongewenste e-mail dit soort e-mails in deze map zet. Als u niet zeker weet of uw logische app correct wordt uitgevoerd, kunt u [Problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md) raadplegen.

Gefeliciteerd, u hebt nu een logische app gemaakt en uitgevoerd die informatie in Azure, Microsoft-services en andere SaaS-apps integreert.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de voor beeld-logische app niet meer nodig hebt, verwijdert u de resource groep die uw logische app en gerelateerde resources bevat. 

1. Ga in het Azure-hoofdmenu naar **Resourcegroepen** en selecteer de resourcegroep voor uw logische app.

1. Selecteer **overzicht** > **resource groep verwijderen**in het menu resource groep. 

   !["Overzicht" > "Resourcegroep verwijderen"](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Voer de naam van de resource groep in als bevestiging en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een logische app gemaakt die goedkeuringen voor adressenlijstaanvragen beheert. Leer nu hoe u een logische app kunt bouwen die e-mailbijlagen verwerkt en opslaat door integratie van Azure-diensten, zoals Azure Storage en Azure Functions.

> [!div class="nextstepaction"]
> [E-mailbijlagen verwerken](../logic-apps/tutorial-process-email-attachments-workflow.md)
