---
title: Geautomatiseerde werkstromen op basis van goedkeuring bouwen
description: Zelfstudie - Een op goedkeuring gebaseerde geautomatiseerde werkstroom maken die abonnementen op mailinglijsten verwerkt met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: 7d7f573e5b18e6e0e63d3275aecefe408a9143fb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75456616"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Zelfstudie: Geautomatiseerde werkstromen op basis van goedkeuring maken met Azure Logic Apps

In deze zelfstudie ziet u hoe u een [logische app](../logic-apps/logic-apps-overview.md) maken die een op goedkeuring gebaseerde werkstroom automatiseert. Met name deze logische app verwerkt abonnementsaanvragen voor een mailinglijst die wordt beheerd door de [MailChimp-service.](https://mailchimp.com/) Deze logische app bewaakt een e-mailaccount voor deze aanvragen, verzendt deze aanvragen voor goedkeuring en voegt goedgekeurde leden toe aan de adressenlijst.

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

![Overzicht van voltooide logica-apps op hoog niveau](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement hebt, [meldt u zich aan voor een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

* Een MailChimp-account met de naam 'test-leden-ML' waar uw logische app e-mailadressen voor goedgekeurde leden kan toevoegen. Als je geen account hebt, [meld je je aan voor een gratis account](https://login.mailchimp.com/signup/)en leer je hoe je een [MailChimp-lijst maakt.](https://us17.admin.mailchimp.com/lists/#)

* Een e-mailaccount in Office 365 Outlook of Outlook.com, dat goedkeuringswerkstromen ondersteunt. In dit artikel wordt gebruikgemaakt van Outlook van Office 365. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Selecteer in het hoofdmenu van Azure de optie Een > **logica-app**voor > **bronintegratie** **maken**.

   ![Uw nieuwe logic-app-bron maken](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. In het menu **Logische app maken** geeft u de informatie over uw logische app op zoals hier wordt weergegeven en beschreven. Als u gereed bent, selecteert u **Maken**.

   ![Informatie geven over uw logische app](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | LA-MailingList | De naam van uw logische app, die alleen letters,`-`cijfers,`_`koppeltekens (`(`), `)`onderstreept (`.`), haakjes ( , en perioden ( ). In dit voorbeeld wordt "LA-MailingList" gebruikt. |
   | **Abonnement** | <*naam van uw Azure-abonnement*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | LA-MailingList-RG | De naam voor de [Azure-brongroep](../azure-resource-manager/management/overview.md), die wordt gebruikt om gerelateerde resources te ordenen. In dit voorbeeld wordt "LA-MailingList-RG" gebruikt. |
   | **Locatie** | VS - west | THet gebied waar u uw logica-app-informatie opslaan. In dit voorbeeld wordt "West US" gebruikt. |
   | **Logboekanalyse** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u op de werkbalk Azure de optie **Meldingen** > **Ga naar bron** voor uw geïmplementeerde logica-app.

   ![Naar uw nieuwe logic app-bron gaan](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   U ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps Designer opent en toont een pagina met een introductievideo en veelgebruikte triggers en logische app-patronen. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Sjabloon voor lege logica-apps selecteren](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Voeg vervolgens een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die luistert naar binnenkomende e-mailberichten met abonnementaanvragen. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer een specifieke gebeurtenis plaatsvindt of wanneer nieuwe gegevens aan een specifieke voorwaarde voldoen. Bekijk [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie.

## <a name="add-trigger-to-monitor-emails"></a>Trigger voor het bewaken van e-mailberichten toevoegen

1. Voer in het zoekvak Logic App `when email arrives` Designer in het zoekvak in als filter. Selecteer in de lijst **Triggers** de **trigger Wanneer een nieuwe e-mail wordt geactiveerd** voor uw e-mailprovider.

   In dit voorbeeld wordt de Office 365 Outlook-trigger gebruikt:

   ![Selecteer trigger voor 'Wanneer er een nieuwe e-mail binnenkomt' voor e-mailprovider](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Voor werk- of schoolaccounts van Azure selecteert u Outlook van Office 365.
   * Selecteer Outlook.com voor persoonlijke Microsoft-accounts.

1. Meld u desgevraagd aan bij uw e-mailaccount met uw referenties, zodat Logic Apps een verbinding met uw e-mailaccount kan maken.

1. Geef in de trigger de criteria op voor het controleren van alle nieuwe e-mail.

   1. Geef de map, interval en frequentie voor het controleren van e-mails op.

      ![Geef map, interval en frequentie voor het controleren van e-mails op](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Eigenschap | Waarde | Beschrijving |
      |----------|-------|-------------|
      | **Map** | `Inbox` | De te bewaken e-mailmap |
      | **Interval** | `1` | Het aantal intervallen dat tussen controles moet worden gewacht |
      | **Frequentie** | `Hour` | Tijdseenheid die voor het terugkeerpatroon wordt gebruikt |
      ||||

   1. Voeg nu een andere eigenschap toe aan de trigger, zodat u filteren op de onderwerpregel van e-mail. Open de **lijst Nieuwe parameter toevoegen**en selecteer de eigenschap **Onderwerpfilter.**

      ![Eigenschap 'Onderwerpfilter' toevoegen om te activeren](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Zie de [office 365 Outlook-connectorverwijzing](https://docs.microsoft.com/connectors/office365/) of de verwijzing naar de [Outlook.com-connector](https://docs.microsoft.com/connectors/outlook/)voor meer informatie over de eigenschappen van deze trigger.

   1. Voer de tekst in nadat de eigenschap in de trigger is weergegeven:`subscribe-test-members-ML`

      ![Tekst invoeren voor de eigenschap 'Onderwerpfilter'.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Als u de details van de trigger voorlopig wilt verbergen, klikt u op de titelbalk van de trigger.

   ![Shape samenvouwen om details te verbergen](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

Uw logische app is nu live, maar doet niets behalve uw binnenkomende e-mail controleren. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="send-approval-email"></a>Goedkeurings-e-mail verzenden

Nu u een trigger hebt, kunt u een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toevoegen waarmee een e-mailbericht wordt verzonden om de aanvraag goed te keuren of af te wijzen.

1. Selecteer Onder de trigger de optie **Nieuwe stap**. 

1. Voer **onder Een actie kiezen**in `approval` het zoekvak in als filter. Selecteer in de lijst met acties de **actie E-mail verzenden** voor uw e-mailprovider. 

   In dit voorbeeld wordt de actie Office 365 Outlook gebruikt:

   ![De actie 'Goedkeuringse-mail verzenden' selecteren](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Geef de informatie over deze actie zoals beschreven: 

   ![Eigenschappen van goedkeuringse-mail verzenden](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Aan** | <*uw e-mailadres*> | Het e-mailadres van de fiatteur. Voor testdoeleinden kunt u uw eigen adres gebruiken. In dit voorbeeldsophia.owen@fabrikam.comwordt het fictieve e-mailadres " gebruikt. |
   | **Onderwerp** | `Approve member request for test-members-ML` | Een beschrijvend e-mailonderwerp |
   | **Gebruikersopties** | `Approve, Reject` | De antwoordopties die de goedkeurder kan selecteren. Standaard kan de goedkeurder 'Goedkeuren' of 'Weigeren' als antwoord selecteren. |
   ||||

   Negeer vooralsnog de lijst met dynamische inhoud die wordt weergegeven wanneer u in specifieke bewerkingsvakken klikt. Met deze lijst u de beschikbare uitvoer selecteren van eerdere acties die u gebruiken als invoer in uw werkstroom.

   Zie de [office 365 Outlook-connectorverwijzing](https://docs.microsoft.com/connectors/office365/) of de verwijzing naar de [Outlook.com-connector](https://docs.microsoft.com/connectors/outlook/)voor meer informatie over de eigenschappen van deze actie.
 
1. Sla uw logische app op.

Voeg vervolgens een voorwaarde toe om het geselecteerde antwoord van de goedkeurder te controleren.

## <a name="check-approval-response"></a>Goedkeuringsantwoord controleren

1. Selecteer onder de **actie Goedkeuringse-mail verzenden** de optie **Nieuwe stap**".

1. Selecteer **Ingebouwde**actie onder **Een actie kiezen**. Voer in het `condition` zoekvak in als filter. Selecteer in de lijst met acties de actie **Voorwaarde.**

   ![De actie 'Voorwaarde' zoeken en selecteren](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Geef de voorwaarde een naam met een betere beschrijving.

   1. Selecteer op de titelbalk van de voorwaarde de knop **ellips** (**...**) > **Naam wijzigen**.

      ![Beschrijving van de naam van de voorwaarde wijzigen](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Verander de naam van uw voorwaarde in deze beschrijving: `If request approved`

1. Stel een voorwaarde die controleert of de goedgekeurde goedkeuring is geselecteerd **voor Goedkeuren**.

   1. Klik in de voorwaarde in het vak **Een waarde kiezen** aan de linkerkant van de voorwaarde.

   1. Selecteer de eigenschap **SelectedOption** in de lijst met dynamische inhoud die wordt weergegeven onder **Goedkeuringse-mail verzenden.**

      ![Selecteer in de lijst met dynamische inhoud de optie 'Geselecteerde optie'.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Selecteer in het middelste vergelijkingsvak de **is gelijk aan** operator.

   1. Voer in het vak **Een waarde kiezen** aan de rechterkant van de voorwaarde de tekst in:`Approve`

      Wanneer u klaar bent, ziet de voorwaarde er als volgt uit:

      ![Voltooide voorwaarde voor goedgekeurd voorbeeld](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Sla uw logische app op.

Geef vervolgens de actie op die uw logische app uitvoert wanneer de fiatteur de aanvraag goedkeurt. 

## <a name="add-member-to-mailchimp-list"></a>Lid toevoegen aan MailChimp-lijst

Voeg nu een actie toe die het goedgekeurde lid toevoegt aan uw mailinglijst.

1. Selecteer Een **actie toevoegen**in de optie **Als waar** in de voorwaarde .

1. Voer onder Een `mailchimp` actie **kiezen**in als filter en selecteer de actie Lid toevoegen aan **lijst.**

   ![De actie 'Lid toevoegen aan lijst' selecteren](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Als je wordt gevraagd om toegang te krijgen tot je MailChimp-account, meld je dan aan met je MailChimp-referenties.

1. Geef informatie over deze actie zoals hier weergegeven en beschreven:

   ![Informatie opgeven voor 'Add member to list'](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **List Id (Lijst-id)** | Ja | `test-members-ML` | De naam voor uw MailChimp mailinglijst. In dit voorbeeld wordt gebruik gemaakt van "test-members-ML". |
   | **Status** | Ja | `subscribed` | Selecteer de abonnementsstatus voor het nieuwe lid. In dit voorbeeld wordt "geabonneerd" gebruikt. <p>Zie voor meer informatie [Manage subscribers with the MailChimp API (Abonnees beheren met de MailChimp-API)](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **E-mailadres** | Ja | <*e-mailadres voor nieuw lid-lid*> | Selecteer in de lijst dynamische inhoud **Van** onder **Wanneer een nieuwe e-mail wordt binnengekomen**, die wordt weergegeven in het e-mailadres van het nieuwe lid. |
   ||||

   Zie de verwijzing naar de [MailChimp-connector](https://docs.microsoft.com/connectors/mailchimp/)voor meer informatie over de eigenschappen van deze actie.

1. Sla uw logische app op.

Voeg vervolgens een voorwaarde toe waarmee u kunt controleren of het nieuwe lid met succes aan uw adressenlijst is toegevoegd. Op die manier meldt uw logische app u of de bewerking is geslaagd of mislukt.

## <a name="check-for-success-or-failure"></a>Controleren op slagen of mislukken

1. Selecteer in de optie **Als als** onder de actie Lid toevoegen **aan lijst** de optie Een **actie toevoegen**.

1. Selecteer **Ingebouwde**actie onder **Een actie kiezen**. Voer in het `condition` zoekvak in als filter. Selecteer **Voorwaarde**in de lijst met acties .

1. Wijzig de naam van de voorwaarde met deze beschrijving: `If add member succeeded`

1. Maak een voorwaarde die controleert of het goedgekeurde lid met succes aan uw adressenlijst is toegevoegd of niet:

   1. Klik in de voorwaarde in het vak **Een waarde kiezen,** dat zich aan de linkerkant van de voorwaarde bevindt. Selecteer in de lijst dynamische inhoud onder **Lid toevoegen aan lijst**de eigenschap **Status.**

      Uw toestand ziet er bijvoorbeeld als volgt uit:

      ![Onder ‘Lid toevoegen aan lijst’ selecteert u ‘Status’](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Selecteer in het middelste vergelijkingsvak de **is gelijk aan** operator.

   1. Voer in het vak **Een waarde kiezen** aan de rechterkant van de voorwaarde de tekst in:`subscribed`

      Wanneer u klaar bent, ziet de voorwaarde er als volgt uit:

      ![Voltooide voorwaarde voor geabonneerd bijvoorbeeld](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Stel vervolgens de e-mailberichten in die moeten worden verzonden als het goedgekeurde lid met succes aan uw adressenlijst is toegevoegd of niet.

## <a name="send-email-if-member-added"></a>E-mailbericht verzenden als lid is toegevoegd

1. Selecteer onder de voorwaarde **Als lid toevoegen geslaagd** in de optie Als **als** de optie Een **actie toevoegen**.

   ![Selecteer in de vertakking 'Als waar' de optie 'Een actie toevoegen'](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Voer **onder Een actie kiezen**in `outlook send email` het zoekvak in als filter en selecteer de actie **Een e-mail verzenden.**

   ![Actie 'Een e-mail verzenden' toevoegen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Wijzig de naam van de actie met deze beschrijving: `Send email on success`

1. Geef informatie voor deze actie op zoals weergegeven en beschreven:

   ![Informatie opgeven voor e-mail bij succes](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Aan** | Ja | <*uw e-mailadres*> | Het e-mailadres waarnaar het bericht bij succes moet worden verzonden. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   | **Onderwerp** | Ja | <*onderwerp-voor-succes-e-mail*> | Het onderwerp voor het e-mailbericht bij succes. Voer voor deze zelfstudie deze tekst in: <p>`Success! Member added to "test-members-ML": ` <p>Selecteer in de lijst dynamische inhoud onder **Lid toevoegen aan lijst**de eigenschap **E-mailadres.** |
   | **Hoofdtekst** | Ja | <*body-for-success-email*> | De hoofdtekst voor het e-mailbericht bij succes. Voer voor deze zelfstudie deze tekst in: <p>`New member has joined "test-members-ML":` <p>Selecteer in de lijst met dynamische inhoud de eigenschap **E-mailadres.** <p>Voer in de volgende rij de volgende tekst in:`Member opt-in status: ` <p> Selecteer in de lijst dynamische inhoud onder **Lid toevoegen aan lijst**de eigenschap **Status.** |
   |||||

1. Sla uw logische app op.

## <a name="send-email-if-member-not-added"></a>E-mailbericht verzenden als lid niet is toegevoegd

1. Selecteer onder de voorwaarde **Als lid toevoegen geslaagd** in de optie Als **onwaar** **vervoegt**de optie Een actie toevoegen .

   ![Selecteer in de vertakking 'Als fout' de optie 'Een actie toevoegen'](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Voer **onder Een actie kiezen**in `outlook send email` het zoekvak in als filter en selecteer de actie **Een e-mail verzenden.**

   ![Actie voor ‘Een e-mail verzenden’ toevoegen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Wijzig de naam van de actie met deze beschrijving: `Send email on failure`

1. Geef informatie over deze actie zoals hier weergegeven en beschreven:

   ![Informatie opgeven voor e-mail bij mislukken](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Aan** | Ja | <*uw e-mailadres*> | Het e-mailadres waarnaar het bericht bij mislukken moet worden verzonden. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   | **Onderwerp** | Ja | <*onderwerp-voor-storing-e-mail*> | Het onderwerp voor het e-mailbericht bij mislukken. Voer voor deze zelfstudie deze tekst in: <p>`Failed, member not added to "test-members-ML": ` <p>Selecteer in de lijst dynamische inhoud onder **Lid toevoegen aan lijst**de eigenschap **E-mailadres.** |
   | **Hoofdtekst** | Ja | <*body-for-failure-email*> | De hoofdtekst voor het e-mailbericht bij mislukken. Voer voor deze zelfstudie deze tekst in: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Sla uw logische app op. 

Test vervolgens uw logische app, die er nu bijna net zo uitziet als dit voorbeeld:

![Voorbeeld van voltooide logische app-werkstroom](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

1. Stuur uzelf een e-mailbericht met een aanvraag om lid te worden van uw adressenlijst. Wacht totdat de aanvraag in uw Postvak IN aankomt.

1. Als u uw logische app handmatig wilt starten, selecteert u op de werkbalkbalk van de ontwerper de optie **Uitvoeren**. 

   Als uw e-mail een onderwerp heeft dat overeenkomt met het onderwerpfilter van de trigger, stuurt uw logische app u een e-mail om de abonnementaanvraag goed te keuren.

1. Selecteer **Goedkeuren in**de goedkeuringse-mail .

1. Als het e-mailadres van de abonnee niet voorkomt in uw adressenlijst, voegt uw logische app het e-mailadres van die persoon toe en stuurt u een e-mail zoals dit voorbeeld:

   ![Voorbeeld e-mail - succesvol abonnement](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Als uw logische app de abonnee niet kan toevoegen, krijgt u een e-mail als in dit voorbeeld:

   ![Voorbeeld e-mail - mislukt abonnement](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Als u geen een e-mailberichten ontvangt, controleert u de map met ongewenste e-mail. Het is mogelijk dat uw filter voor ongewenste e-mail dit soort e-mails in deze map zet. Als u niet zeker weet of uw logische app correct wordt uitgevoerd, kunt u [Problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md) raadplegen.

Gefeliciteerd, u hebt nu een logische app gemaakt en uitgevoerd die informatie in Azure, Microsoft-services en andere SaaS-apps integreert.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de voorbeeldlogica-app niet meer nodig hebt, verwijdert u de brongroep die uw logische app en gerelateerde bronnen bevat. 

1. Ga in het Azure-hoofdmenu naar **Resourcegroepen** en selecteer de resourcegroep voor uw logische app.

1. Selecteer resourcegroep **Overzicht** > verwijderen in het menu**Resourcegroep verwijderen**. 

   !["Overzicht" > "Resourcegroep verwijderen"](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Voer de naam van de brongroep in als bevestiging en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een logische app gemaakt die goedkeuringen voor adressenlijstaanvragen beheert. Leer nu hoe u een logische app kunt bouwen die e-mailbijlagen verwerkt en opslaat door integratie van Azure-diensten, zoals Azure Storage en Azure Functions.

> [!div class="nextstepaction"]
> [E-mailbijlagen verwerken](../logic-apps/tutorial-process-email-attachments-workflow.md)
