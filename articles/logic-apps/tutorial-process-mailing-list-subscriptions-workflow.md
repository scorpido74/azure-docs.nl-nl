---
title: Geautomatiseerde op goedkeuring gebaseerde werkstromen bouwen
description: 'Zelfstudie: geautomatiseerde op goedkeuring gebaseerde werkstroom maken die mailinglijstabonnementen verwerkt met behulp van Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: d9d2f29ffc34c203e5f3b3ebf094e73fb9cdfb75
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132395"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Zelfstudie: Geautomatiseerde op goedkeuring gebaseerde workflows maken met behulp van Azure Logic Apps

In deze zelfstudie leert u hoe u een [logische app](../logic-apps/logic-apps-overview.md) bouwt waarmee een op goedkeuring gebaseerde workflow wordt geautomatiseerd. Deze logische app verwerkt specifiek abonnementsaanvragen voor een mailinglijst die wordt beheerd door de [MailChimp](https://mailchimp.com/)-service. Deze logische app bewaakt een e-mailaccount voor deze aanvragen, verzendt deze aanvragen voor goedkeuring en voegt goedgekeurde leden toe aan de adressenlijst.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een lege, logische app maken.
> * Een trigger toevoegen waarmee e-mailberichten op abonnementaanvragen worden gecontroleerd.
> * Een actie toevoegen waarmee e-mailberichten worden verzonden om deze aanvragen goed te keuren of af te wijzen.
> * Een voorwaarde toevoegen voor het controleren van het goedkeuringsantwoord.
> * Een actie toevoegen waarmee goedgekeurde leden worden toegevoegd aan de adressenlijst.
> * Een voorwaarde toevoegen waarmee wordt gecontroleerd of deze leden met succes aan de lijst zijn toegevoegd.
> * Een voorwaarde toevoegen waarmee e-mailberichten worden verzonden om te bevestigen of deze leden met succes aan de lijst zijn toegevoegd.

Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Overzicht van een voltooide logische app op hoog niveau](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [moet u zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

* Een MailChimp-account dat een lijst bevat met de naam test-leden-ML waaraan uw logische app e-mailadressen voor goedgekeurde leden kan toevoegen. Als u geen account hebt, kunt u zich [aanmelden voor een gratis account](https://login.mailchimp.com/signup/) en leren [hoe u een MailChimp-lijst kunt maken](https://us17.admin.mailchimp.com/lists/#).

* Een e-mailaccount in Office 365 Outlook of Outlook.com, die goedkeuringswerkstromen. In dit artikel wordt gebruikgemaakt van Outlook van Office 365. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Selecteer in het hoofdmenu van Azure **Een resource maken** > **Integratie** > **Logische app**.

   ![Uw nieuwe logische app-resource maken](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. In het menu **Logische app maken** geeft u de informatie over uw logische app op zoals hier wordt weergegeven en beschreven. Als u gereed bent, selecteert u **Maken**.

   ![Geef informatie op over uw logische app](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | LA-MailingList | De naam van uw logische app mag alleen letters, cijfers, afbreekstreepjes (`-`), onderstrepingstekens (`_`), haakjes (`(`, `)`) en punten (`.`) bevatten. In dit voorbeeld wordt LA-MailingList gebruikt. |
   | **Abonnement** | <*your-Azure-subscription-name*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | LA-MailingList-RG | De naam van de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) die wordt gebruikt om verwante resources te organiseren. In dit voorbeeld wordt LA-MailingList-RG gebruikt. |
   | **Locatie** | VS - west | De regio waarin informatie over uw logische app moet worden opgeslagen. In dit voorbeeld wordt 'US - west' gebruikt. |
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u in de Azure-werkbalk de optie **Meldingen** > **Resources openen** voor uw geïmplementeerde logische app.

   ![Ga naar uw nieuwe logische app-resource](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   U kunt ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps-ontwerpfunctie wordt geopend en u ziet een pagina met een inleidende video, veelgebruikte triggers en patronen voor logische apps. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Selecteer een leeg sjabloon voor uw logische app](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Voeg vervolgens een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die luistert naar binnenkomende e-mailberichten met abonnementaanvragen. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer een bepaalde gebeurtenis plaatsvindt of wanneer nieuwe gegevens voldoen aan een bepaalde voorwaarde. Bekijk [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie.

## <a name="add-trigger-to-monitor-emails"></a>Trigger voor het bewaken van e-mailberichten toevoegen

1. Voer in de ontwerpfunctie van de logische app in het zoekvenster `when email arrives` in als uw filter. Selecteer in de lijst **Triggers** de trigger **Wanneer er nieuwe e-mail binnenkomt** voor uw e-mailprovider.

   In dit voorbeeld wordt de trigger Outlook van Office 365 gebruikt:

   ![Selecteer de trigger Wanneer er nieuwe e-mail binnenkomt voor e-mail mailprovider](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Voor werk- of schoolaccounts van Azure selecteert u Outlook van Office 365.
   * Selecteer Outlook.com voor persoonlijke Microsoft-accounts.

1. Als u hierom wordt gevraagd, meldt u zich aan bij uw e-mailaccount met uw referenties, zodat Logic Apps een verbinding met uw e-mailaccount kan maken.

1. Geef in de trigger de criteria op voor het controleren van alle nieuwe e-mail.

   1. Geef de map, interval en frequentie voor het controleren van e-mails op.

      ![Geef map, interval en frequentie voor het controleren van e-mails op](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Eigenschap | Waarde | Beschrijving |
      |----------|-------|-------------|
      | **Map** | `Inbox` | De te bewaken e-mailmap |
      | **Interval** | `1` | Het aantal intervallen dat tussen controles moet worden gewacht |
      | **Frequentie** | `Hour` | Tijdseenheid die voor het terugkeerpatroon wordt gebruikt |
      ||||

   1. Voeg nu een andere eigenschap toe aan de trigger zodat u kunt filteren op de onderwerpregel van het e-mailbericht. Open de optie **Nieuwe parameterlijst toevoegen** en selecteer de eigenschap **Onderwerpfilter**.

      ![Voeg de eigenschap Onderwerpfilter aan de trigger toe](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Zie voor meer informatie over de eigenschappen van deze trigger de [Office 365 Outlook-connectorreferentie](/connectors/office365/) of de [Outlook.com-connectorreferentie](/connectors/outlook/).

   1. Nadat de eigenschap in de trigger verschijnt, voert u deze tekst in: `subscribe-test-members-ML`

      ![Voer tekst in voor de eigenschap Onderwerpfilter](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Als u de details van de trigger voorlopig wilt verbergen, klikt u op de titelbalk van de trigger.

   ![Shape samenvouwen om details te verbergen](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Uw logische app is nu live, maar doet niets behalve uw binnenkomende e-mail controleren. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="send-approval-email"></a>Goedkeurings-e-mail verzenden

Nu u een trigger hebt, kunt u een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toevoegen waarmee een e-mailbericht wordt verzonden om de aanvraag goed te keuren of af te wijzen.

1. Selecteer onder de trigger de optie **Nieuwe stap**. 

1. Voer in het zoekvak onder **Kies een actie** `approval` in als uw filter. Selecteer in de lijst met acties de actie **Goedkeurings-e-mail verzenden** voor uw e-mailprovider. 

   In dit voorbeeld wordt de actie Outlook van Office 365 gebruikt:

   ![Selecteer de actie Goedkeuring keurings-e-mail verzenden](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Geef de informatie over deze actie op zoals beschreven: 

   ![Eigenschappen voor verzenden van goedkeurings-e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Aan** | <*uw-e-mailadres*> | Het e-mailadres van de fiatteur. Voor testdoeleinden kunt u uw eigen adres gebruiken. In dit voorbeeld wordt het fictieve e-mailadres sophia.owen@fabrikam.com gebruikt. |
   | **Onderwerp** | `Approve member request for test-members-ML` | Een beschrijvend e-mailonderwerp |
   | **Gebruikersopties** | `Approve, Reject` | De antwoordopties die de fiatteur kan kiezen. De fiatteur kan standaard Goedkeuren of Afwijzen als antwoord kiezen. |
   ||||

   Negeer voorlopig de dynamische inhoudlijst die verschijnt wanneer u in specifieke tekstvakken klikt. In deze lijst kunt u beschikbare uitvoer van vorige acties selecteren die u kunt gebruiken als invoer in uw werkstroom.

   Zie voor meer informatie over de eigenschappen van deze actie de [Office 365 Outlook-connectorreferentie](/connectors/office365/) of de [Outlook.com-connectorreferentie](/connectors/outlook/).
 
1. Sla uw logische app op.

Vervolgens voegt u een voorwaarde toe om het door de fiatteur geselecteerde antwoord te controleren.

## <a name="check-approval-response"></a>Goedkeuringsantwoord controleren

1. Selecteer onder de actie **Goedkeurings-e-mail** de optie **Nieuwe stap**.

1. Selecteer onder **Kies een actie**de optie **Ingebouwd**. Voer in het zoekvak `condition` als uw filter in. Selecteer in de lijst met acties de actie **Voorwaarde**.

   ![Zoek en selecteer de actie Voorwaarde](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Geef de voorwaarde een naam met een betere beschrijving.

   1. Selecteer op de titelbalk van de voorwaarde het **beletselteken** ( **...** ) > **Naam wijzigen**.

      ![Naam van voorwaardebeschrijving wijzigen](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Verander de naam van uw voorwaarde in deze beschrijving: `If request approved`

1. Bouw een voorwaarde op waarmee wordt gecontroleerd of de fiatteur **Goedkeuren** heeft geselecteerd.

   1. Klik in de voorwaarde in het vak **Kies een waarde** aan de linkerkant van de voorwaarde.

   1. Selecteer in de lijst met dynamische inhoud die verschijnt onder **Goedkeurings-e-mail verzenden** de eigenschap **SelectedOption**.

      ![Selecteer in de lijst met dynamische inhoud de eigenschap SelectedOption](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Selecteer in het middelste vergelijkingsvak de operator **is gelijk aan**.

   1. Voer in het vak **Kies een waarde** aan de rechterkant van de voorwaarde deze tekst in: `Approve`

      Wanneer u klaar bent, ziet de voorwaarde eruit zoals in dit voorbeeld:

      ![Voorbeeld van voltooide voorwaarde voor Goedgekeurd](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Sla uw logische app op.

Geef vervolgens de actie op die uw logische app uitvoert wanneer de fiatteur de aanvraag goedkeurt. 

## <a name="add-member-to-mailchimp-list"></a>Lid toevoegen aan MailChimp-lijst

Voeg nu een actie toe waarmee het goedgekeurde lid wordt toegevoegd aan uw mailinglijst.

1. Selecteer **Een actie toevoegen** in de vertakking **Indien waar** van de voorwaarde.

1. Voer onder **Kies een actie** `mailchimp` in als uw filter en selecteer de actie **Lid toevoegen aan lijst**.

   ![Selecteer de actie Lid toevoegen aan lijst](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Als u wordt gevraagd uw MailChimp-account te openen, meldt u zich aan met uw MailChimp-referenties.

1. Geef informatie over deze actie op zoals hier wordt weergegeven en beschreven:

   ![Informatie opgeven voor 'Add member to list'](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **List Id (Lijst-id)** | Ja | `test-members-ML` | De naam voor uw MailChimp-mailinglijst. In dit voorbeeld wordt test-members-ML gebruikt. |
   | **Status** | Ja | `subscribed` | Selecteer de abonnementsstatus voor het nieuwe lid. In dit voorbeeld wordt subscribed gebruikt. <p>Zie voor meer informatie [Manage subscribers with the MailChimp API (Abonnees beheren met de MailChimp-API)](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **Email Address (E-mailadres)** | Ja | <*e-mailadres-nieuw-lid*> | Selecteer in de lijst met dynamische inhoud **Van** onder **Wanneer er een nieuwe e-mail binnenkomt**, waardoor het e-mailadres voor het nieuwe lid wordt doorgegeven. |
   ||||

   Zie voor meer informatie over de eigenschappen van deze actie de [MailChimp-connectorreferentie](/connectors/mailchimp/).

1. Sla uw logische app op.

Voeg vervolgens een voorwaarde toe waarmee u kunt controleren of het nieuwe lid met succes aan uw adressenlijst is toegevoegd. Op die manier meldt uw logische app u of de bewerking is geslaagd of mislukt.

## <a name="check-for-success-or-failure"></a>Controleren op slagen of mislukken

1. Selecteer in de vertakking **Indien waar** onder de actie **Lid toevoegen aan lijst** de optie **Een actie toevoegen**.

1. Selecteer onder **Kies een actie**de optie **Ingebouwd**. Voer in het zoekvak `condition` als uw filter in. Selecteer in de lijst Acties de optie **Voorwaarde**.

1. Wijzig de naam van de voorwaarde met deze beschrijving: `If add member succeeded`

1. Maak een voorwaarde die controleert of het goedgekeurde lid met succes aan uw adressenlijst is toegevoegd of niet:

   1. Klik in de voorwaarde op het vakje **Kies een waarde** aan de linkerkant van de voorwaarde. Selecteer in de lijst met dynamische inhoud onder **Lid toevoegen aan lijst** de eigenschap **Status**.

      Uw voorwaarde ziet eruit zoals in dit voorbeeld:

      ![Onder ‘Lid toevoegen aan lijst’ selecteert u ‘Status’](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Selecteer in het middelste vergelijkingsvak de operator **is gelijk aan**.

   1. Voer in het vak **Kies een waarde** aan de rechterkant van de voorwaarde deze tekst in: `subscribed`

      Wanneer u klaar bent, ziet de voorwaarde eruit zoals in dit voorbeeld:

      ![Voorbeeld van voltooide voorwaarde voor Geabonneerd](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Stel vervolgens de e-mailberichten in die moeten worden verzonden als het goedgekeurde lid met succes aan uw adressenlijst is toegevoegd of niet.

## <a name="send-email-if-member-added"></a>E-mailbericht verzenden als lid is toegevoegd

1. Selecteer onder de voorwaarde **Indien toevoegen van lid is geslaagd** in de vertakking **Indien waar** de optie **Een actie toevoegen**.

   ![Selecteer in de vertakking Indien waar de optie Een actie toevoegen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Voer onder **Kies een actie** in het zoekvak `outlook send email` in als uw filter en selecteer de actie **Een e-mail verzenden**.

   ![Voeg de actie Een e-mail verzenden toe](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Wijzig de naam van de actie met deze beschrijving: `Send email on success`

1. Geef informatie voor deze actie op zoals weergegeven en beschreven:

   ![Informatie opgeven voor e-mail bij succes](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Aan** | Ja | <*uw-e-mailadres*> | Het e-mailadres waarnaar het bericht bij succes moet worden verzonden. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   | **Onderwerp** | Ja | <*onderwerp-voor-succes-e-mail*> | Het onderwerp voor het e-mailbericht bij succes. Voer voor deze zelfstudie deze tekst in: <p>`Success! Member added to "test-members-ML": ` <p>Selecteer in de lijst met dynamische inhoud onder **Lid toevoegen aan lijst** de eigenschap **E-mailadres**. |
   | **Hoofdtekst** | Ja | <*hoofdtekst-voor-succes-e-mail*> | De hoofdtekst voor het e-mailbericht bij succes. Voer voor deze zelfstudie deze tekst in: <p>`New member has joined "test-members-ML":` <p>Selecteer in de lijst met dynamische inhoud de eigenschap **E-mailadres**. <p>Voer deze tekst in op de volgende rij: `Member opt-in status: ` <p> Selecteer in de lijst met dynamische inhoud onder **Lid toevoegen aan lijst** de eigenschap **Status**. |
   |||||

1. Sla uw logische app op.

## <a name="send-email-if-member-not-added"></a>E-mailbericht verzenden als lid niet is toegevoegd

1. Selecteer onder de voorwaarde **Indien toevoegen van lid is geslaagd** in de vertakking **Indien niet waar** de optie **Een actie toevoegen**.

   ![Selecteer in de vertakking Indien niet waar de optie Een actie toevoegen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Voer onder **Kies een actie** in het zoekvak `outlook send email` in als uw filter en selecteer de actie **Een e-mail verzenden**.

   ![Actie voor ‘Een e-mail verzenden’ toevoegen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Wijzig de naam van de actie met deze beschrijving: `Send email on failure`

1. Geef informatie over deze actie op zoals hier wordt weergegeven en beschreven:

   ![Informatie opgeven voor e-mail bij mislukken](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Aan** | Ja | <*uw-e-mailadres*> | Het e-mailadres waarnaar het bericht bij mislukken moet worden verzonden. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   | **Onderwerp** | Ja | <*onderwerp-voor-e-mail-bij-mislukken*> | Het onderwerp voor het e-mailbericht bij mislukken. Voer voor deze zelfstudie deze tekst in: <p>`Failed, member not added to "test-members-ML": ` <p>Selecteer in de lijst met dynamische inhoud onder **Lid toevoegen aan lijst** de eigenschap **E-mailadres**. |
   | **Hoofdtekst** | Ja | <*hoofdtekst-voor-e-mail-bij-mislukken*> | De hoofdtekst voor het e-mailbericht bij mislukken. Voer voor deze zelfstudie deze tekst in: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Sla uw logische app op. 

Test vervolgens uw logische app, die er nu bijna net zo uitziet als dit voorbeeld:

![Voorbeeld van een voltooide werkstroom voor logische app](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

1. Stuur uzelf een e-mailbericht met een aanvraag om lid te worden van uw adressenlijst. Wacht totdat de aanvraag in uw Postvak IN aankomt.

1. Selecteer **Uitvoeren** op de werkbalk in de ontwerper als u de logische app handmatig wilt uitvoeren. 

   Als uw e-mail een onderwerp heeft dat overeenkomt met het onderwerpfilter van de trigger, stuurt uw logische app u een e-mail om de abonnementaanvraag goed te keuren.

1. Selecteer **Goedkeuren** in de goedkeurings-e-mail.

1. Als het e-mailadres van de abonnee niet voorkomt in uw adressenlijst, voegt uw logische app het e-mailadres van die persoon toe en stuurt u een e-mail zoals dit voorbeeld:

   ![Voorbeeld van e-mailbericht - abonnement geslaagd](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Als uw logische app de abonnee niet kan toevoegen, krijgt u een e-mail als in dit voorbeeld:

   ![Voorbeeld van e-mailbericht - abonnement mislukt](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Als u geen een e-mailberichten ontvangt, controleert u de map met ongewenste e-mail. Het is mogelijk dat uw filter voor ongewenste e-mail dit soort e-mails in deze map zet. Als u niet zeker weet of uw logische app correct wordt uitgevoerd, kunt u [Problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md) raadplegen.

Gefeliciteerd, u hebt nu een logische app gemaakt en uitgevoerd die informatie in Azure, Microsoft-services en andere SaaS-apps integreert.

## <a name="clean-up-resources"></a>Resources opschonen

Als u het voorbeeld van de logische app niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat. 

1. Ga in het Azure-hoofdmenu naar **Resourcegroepen** en selecteer de resourcegroep voor uw logische app.

1. Selecteer in het menu van de resourcegroep de optie **Overzicht** > **Resourcegroep verwijderen**. 

   !["Overzicht" > "Resourcegroep verwijderen"](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Voer ter bevestiging de naam van de resourcegroep in en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een logische app gemaakt die goedkeuringen voor adressenlijstaanvragen beheert. Leer nu hoe u een logische app kunt bouwen die e-mailbijlagen verwerkt en opslaat door integratie van Azure-diensten, zoals Azure Storage en Azure Functions.

> [!div class="nextstepaction"]
> [E-mailbijlagen verwerken](../logic-apps/tutorial-process-email-attachments-workflow.md)
