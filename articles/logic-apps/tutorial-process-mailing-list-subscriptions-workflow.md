---
title: Goedkeuringswerkstromen maken voor het verwerken van adressenlijstaanvragen - Azure Logic Apps | Microsoft Docs
description: 'Zelfstudie: het maken van automatische goedkeuringswerkstromen voor het verwerken van adressenlijstabonnementen met Azure Logic Apps'
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
ms.openlocfilehash: 016d004a538a1313ca31f36b46e961098051785c
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051722"
---
# <a name="manage-mailing-list-requests-with-azure-logic-apps"></a>Adressenlijstaanvragen beheren met Azure Logic Apps

Azure Logic Apps helpt u om uw werkstromen te automatiseren en om gegevens te integreren in Azure-services, Microsoft-services, andere SaaS-apps (software als een service) en on-premises systemen. Deze zelfstudie laat zien hoe u een [logische app](../logic-apps/logic-apps-overview.md) kunt bouwen die abonnementsaanvragen verwerkt voor een adressenlijst die wordt beheerd door de [MailChimp](https://mailchimp.com/)-service.
Deze logische app bewaakt een e-mailaccount voor deze aanvragen, verzendt deze aanvragen voor goedkeuring en voegt goedgekeurde leden toe aan de adressenlijst.

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

![Een voltooide logische app op hoog niveau](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-overview.png)

Als u nog geen abonnement op Azure hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een MailChimp-account. Maak een lijst met de naam ‘test-leden-ML’ waaraan uw logische app e-mailadressen voor goedgekeurde leden kan toevoegen. Als u geen account hebt, kunt u zich [aanmelden voor een gratis account](https://login.mailchimp.com/signup/) en leren [hoe u een lijst kunt maken](https://us17.admin.mailchimp.com/lists/#). 

* Een e-mail account met Office 365 Outlook of Outlook.com, dat goedkeurings werk stromen ondersteunt. In dit artikel wordt gebruikgemaakt van Outlook van Office 365. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Kies in het hoofdmenu van Azure **Een resource maken** > **Bedrijfsintegratie** > **Logische app**.

   ![Logische app maken](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app.png)

2. In het menu **Logische app maken** geeft u de informatie over uw logische app op zoals hier wordt weergegeven en beschreven. Als u klaar bent, kiest u **Vastmaken aan dashboard** > **Maken**.

   ![Informatie over logische app opgeven](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Instelling | Value | Beschrijving | 
   | ------- | ----- | ----------- | 
   | **Name** | LA-MailingList | De naam voor uw logische app | 
   | **Abonnement** | <*your-Azure-subscription-name*> | De naam van uw Azure-abonnement | 
   | **Resourcegroep** | LA-MailingList-RG | De naam van de [Azure-resourcegroep](../azure-resource-manager/resource-group-overview.md) die wordt gebruikt om verwante resources te organiseren | 
   | **Location** | US - oost 2 | De regio waar u informatie over uw logische app opslaat | 
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. | 
   |||| 

3. Nadat Azure uw app heeft geïmplementeerd, wordt de Ontwerper van logische apps geopend en ziet u een pagina met een inleidende video en sjablonen voor veelgebruikte patronen voor logische apps. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Sjabloon voor lege logische app kiezen](./media/tutorial-process-mailing-list-subscriptions-workflow/choose-logic-app-template.png)

Voeg vervolgens een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die luistert naar binnenkomende e-mailberichten met abonnementaanvragen.
Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer nieuwe gegevens voldoen aan een bepaalde voorwaarde. Bekijk [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie.

## <a name="add-trigger-to-monitor-emails"></a>Trigger voor het bewaken van e-mailberichten toevoegen

1. Typ 'wanneer e-mail aankomt' in het zoekvak van de ontwerpfunctie. Selecteer de trigger voor uw e-mailprovider: **<*uw-e-mailprovider*> - Wanneer er een nieuwe e-mail binnenkomt**
   
   ![Selecteer deze trigger voor de e-mailprovider: Wanneer een nieuwe e-mail binnenkomt](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Voor werk- of schoolaccounts van Azure selecteert u Outlook van Office 365.
   * Selecteer Outlook.com voor persoonlijke Microsoft-accounts.

2. Als u om uw referenties wordt gevraagd, meldt u zich aan bij uw e-mailaccount, zodat Logic Apps een verbinding met uw e-mailaccount kan maken.

3. Geef nu de criteria op waarop de trigger alle nieuwe e-mail controleert.

   1. Geef de map, interval en frequentie voor het controleren van e-mails op.

      ![Geef map, interval en frequentie voor het controleren van e-mails op](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Instelling | Value | Description | 
      | ------- | ----- | ----------- | 
      | **Map** | Postvak IN | De te bewaken e-mailmap | 
      | **Interval** | 1 | Het aantal intervallen dat tussen controles moet worden gewacht | 
      | **Frequentie** | Uur | De tijdseenheid voor elk interval tussen controles  | 
      |  |  |  | 

   2. Kies **Geavanceerde opties weergeven**. Voer in het vak **Onderwerpfilter** deze tekst in voor de trigger om in het e-mailonderwerp te zoeken: ```subscribe-test-members-ML```

      ![Geavanceerde opties instellen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-advanced-options.png)

4. Als u de details van de trigger voorlopig wilt verbergen, klikt u op de titelbalk van de trigger.

   ![Shape samenvouwen om details te verbergen](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

5. Sla uw logische app op. Kies **Opslaan** op de werkbalk van de ontwerper.

   Uw logische app is nu live, maar doet niets behalve uw binnenkomende e-mail controleren. 
   Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="send-approval-email"></a>Goedkeurings-e-mail verzenden

Nu u een trigger hebt, kunt u een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toevoegen waarmee een e-mailbericht wordt verzonden om de aanvraag goed te keuren of af te wijzen. 

1. Kies bij de trigger **+ Nieuwe stap** > **Een actie toevoegen**. Zoek naar 'goedkeuring' en selecteer deze actie: **<*uw-e-mailprovider*> - Goedkeurings-e-mail verzenden**

   ![Kies ‘<uw-e-mailprovider> - Goedkeurings-e-mail verzenden’](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

2. Geef informatie voor deze actie op zoals weergegeven en beschreven: 

   ![Goedkeurings-e-mailinstellingen instellen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Instelling | Value | Description | 
   | ------- | ----- | ----------- | 
   | **To** | <*e-mail-adres-fiatteur*> | Het e-mailadres van de fiatteur. Voor testdoeleinden kunt u uw eigen adres gebruiken. | 
   | **Gebruikersopties** | Goedkeuren, Afwijzen | De antwoordopties die de fiatteur kan kiezen. Standaard kan de fiatteur ‘Goedkeuren’ of ‘Afwijzen’ kiezen als antwoord. | 
   | **Subject** | Lidmaatschapsaanvraag voor test-leden-ML goedkeuren | Een beschrijvend e-mailonderwerp | 
   |  |  |  | 

   Negeer voorlopig de dynamische inhoudlijst of inline parameterlijst die verschijnt wanneer u in specifieke tekstvakken klikt. 
   In deze lijst kunt u parameters van vorige acties selecteren die u kunt gebruiken als invoer in uw werkstroom. 
   De breedte van uw browser bepaalt welke lijst wordt weergegeven. 
 
4. Sla uw logische app op.

Vervolgens voegt u een voorwaarde toe om het door de fiatteur gekozen antwoord te controleren.

## <a name="check-approval-response"></a>Goedkeuringsantwoord controleren

1. Kies onder de actie **Goedkeurings-e-mail toevoegen** **+ Nieuwe stap** > **Een voorwaarde toevoegen**.

   De voorwaardeshape verschijnt, samen met de beschikbare parameters die u als invoer in uw workflow kunt opnemen. 

2. Geef de voorwaarde een naam met een betere beschrijving.

   1. Kies op de titelbalk van de voorwaarde **de knop met weglatingstekens** ( **...** ) > **Naam wijzigen**.

      Als u browser bijvoorbeeld in een kleine weergave wordt weergegeven:

      ![Naam voorwaarde wijzigen](./media/tutorial-process-mailing-list-subscriptions-workflow/condition-rename.png)

      Als uw browser over het hele scherm wordt weergegeven en de lijst met dynamische inhoud de toegang tot de knop met weglatingstekens blokkeert, sluit u de lijst door in de voorwaarde **Dynamische inhoud toevoegen** te selecteren.

   2. Verander de naam van uw voorwaarde in deze beschrijving: ```If request approved```

3. Bouw een voorwaarde op waarmee wordt gecontroleerd of de fiatteur **Goedkeuren** heeft geselecteerd:

   1. Klik in de voorwaarde in het vak **Een waarde kiezen** aan de linkerkant (brede browserweergave) of aan de bovenkant (smalle browserweergave).
   Selecteer uit de lijst met parameters of de lijst met dynamische inhoud het veld **SelectedOption** onder **Goedkeurings-e-mail verzenden**.

      Als u bijvoorbeeld in de brede weergave werkt, ziet uw voorwaarde eruit zoals in dit voorbeeld:

      ![Selecteer 'SelectedOption' onder 'Goedkeurings-e-mail verzenden'](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   2. Selecteer in het vak voor de vergelijkingsoperator deze operator: **is gelijk aan**

   3. Voer in het vak **Een waarde kiezen** aan de rechterkant (brede weergave) of aan de onderkant (smalle weergave) deze waarde in: ```Approve```

      Wanneer u klaar bent, ziet de voorwaarde eruit zoals in dit voorbeeld:

      ![Voltooide voorwaarde](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

4. Sla uw logische app op.

Geef vervolgens de actie op die uw logische app uitvoert wanneer de fiatteur de aanvraag goedkeurt. 

## <a name="add-member-to-mailchimp-list"></a>Lid toevoegen aan MailChimp-lijst

Voeg nu een actie toe waarmee het goedgekeurde lid wordt toegevoegd aan uw adressenlijst.

1. In de vertakking **Indien waar** van de voorwaarde kiest u **Een actie toevoegen**.
Zoek naar ' MailChimp ' en selecteer deze actie: **MailChimp-lid aan lijst toevoegen**

   ![Selecteer ‘MailChimp - Add member to list’](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

3. Als u wordt gevraagd u aan te melden bij uw MailChimp-account, meldt u zich aan met uw MailChimp-referenties.

4. Geef informatie voor deze actie op zoals hier wordt weergegeven en beschreven:

   ![Informatie opgeven voor 'Add member to list'](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Instelling | Value | Description | 
   | ------- | ----- | ----------- | 
   | **List Id (Lijst-id)** | test-leden-ML | De naam voor uw MailChimp-adressenlijst | 
   | **Status** | subscribed (geabonneerd) | De abonnementstatus voor het nieuwe lid. Zie voor meer informatie [Manage subscribers with the MailChimp API (Abonnees beheren met de MailChimp-API)](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). | 
   | **Email Address (E-mailadres)** | <*e-mailadres-nieuw-lid*> | Selecteer uit de lijst met parameters of de lijst met dynamische inhoud **Van** onder **Wanneer er een nieuwe e-mail binnenkomt**, waardoor het e-mailadres voor het nieuwe lid wordt doorgegeven. 
   |  |  |  | 

5. Sla uw logische app op.

Voeg vervolgens een voorwaarde toe waarmee u kunt controleren of het nieuwe lid met succes aan uw adressenlijst is toegevoegd. Op die manier meldt uw logische app u of de bewerking is geslaagd of mislukt.

## <a name="check-for-success-or-failure"></a>Controleren op slagen of mislukken

1. Selecteer in de tak **Indien waar** onder de actie **Lid toevoegen aan lijst** de optie **Meer...**  > **Een voorwaarde toevoegen**.

2. Wijzig de naam van de voorwaarde met deze beschrijving: ```If add member succeeded```

3. Maak een voorwaarde die controleert of het goedgekeurde lid met succes aan uw adressenlijst is toegevoegd of niet:

   1. Klik in de voorwaarde in het vak **Een waarde kiezen** aan de linkerkant (brede browserweergave) of aan de bovenkant (smalle browserweergave).
   Selecteer in de parameterlijst of de lijst met dynamische inhoud het veld **Status** onder **Lid toevoegen aan lijst**.

      Als u bijvoorbeeld in de brede weergave werkt, ziet uw voorwaarde eruit zoals in dit voorbeeld:

      ![Onder ‘Lid toevoegen aan lijst’ selecteert u ‘Status’](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   2. Selecteer in het vak voor de vergelijkingsoperator deze operator: **is gelijk aan**

   3. Voer in het vak **Een waarde kiezen** aan de rechterkant (brede weergave) of aan de onderkant (smalle weergave) deze waarde in: ```subscribed```

   Wanneer u klaar bent, ziet de voorwaarde eruit zoals in dit voorbeeld:

   ![Voltooide voorwaarde](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Stel vervolgens de e-mailberichten in die moeten worden verzonden als het goedgekeurde lid met succes aan uw adressenlijst is toegevoegd of niet.

## <a name="send-email-if-member-added"></a>E-mailbericht verzenden als lid is toegevoegd

1. Kies in de tak **Indien waar** voor de voorwaarde **If add member succeeded (Indien toevoegen van lid is geslaagd)** **Een actie toevoegen**.

   ![Kies ‘Een actie toevoegen’ in de tak ‘Indien waar’ van de voorwaarde](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

2. Zoek naar ‘outlook e-mail verzenden’ en selecteer deze actie: **<*uw-e-mailprovider*> - Een e-mail verzenden**

   ![Actie voor ‘Een e-mail verzenden’ toevoegen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

3. Wijzig de naam van de actie met deze beschrijving: ```Send email on success```

4. Geef informatie voor deze actie op zoals weergegeven en beschreven:

   ![Informatie opgeven voor e-mail bij succes](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Instelling | Value | Description | 
   | ------- | ----- | ----------- | 
   | **To** | <*uw-e-mailadres*> | Het e-mailadres waarnaar het bericht bij succes moet worden verzonden. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. | 
   | **Subject** | <*subject-for-success-email*> | Het onderwerp voor het e-mailbericht bij succes. Voer voor deze zelfstudie deze tekst in en selecteer het opgegeven veld onder **Lid toevoegen aan lijst** uit de parameterlijst of de lijst met dynamische inhoud: <p>‘Geslaagd! Lid toegevoegd aan ' test-leden-ML ': **E-mail adres**" | 
   | **Hoofdtekst** | <*hoofdtekst-voor-succes-e-mail*> | De hoofdtekst voor het e-mailbericht bij succes. Voer voor deze zelfstudie deze tekst in en selecteer de opgegeven velden onder **Lid toevoegen aan lijst** uit de parameterlijst of de lijst met dynamische inhoud:  <p>' Nieuw lid heeft lid van ' test-leden-ML ': **E-mail adres**"</br>"Opt-in status lid: **Status**" | 
   | | | | 

5. Sla uw logische app op.

## <a name="send-email-if-member-not-added"></a>E-mailbericht verzenden als lid niet is toegevoegd

1. Kies in de tak **Indien onwaar** voor de voorwaarde **If add member succeeded (Indien toevoegen van lid is geslaagd)** **Een actie toevoegen**.

   ![Kies ‘Een actie toevoegen’ in de tak ‘Indien onwaar’ van de voorwaarde](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

2. Zoek naar ‘outlook e-mail verzenden’ en selecteer deze actie: **<*uw-e-mailprovider*> - Een e-mail verzenden**

   ![Actie voor ‘Een e-mail verzenden’ toevoegen](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

3. Wijzig de naam van de actie met deze beschrijving: ```Send email on failure```

4. Geef informatie voor deze actie op zoals hier wordt weergegeven en beschreven:

   ![Informatie opgeven voor e-mail bij mislukken](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Instelling | Value | Description | 
   | ------- | ----- | ----------- | 
   | **To** | <*uw-e-mailadres*> | Het e-mailadres waarnaar het bericht bij mislukken moet worden verzonden. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. | 
   | **Subject** | <*subject-for-failure-email*> | Het onderwerp voor het e-mailbericht bij mislukken. Voer voor deze zelfstudie deze tekst in en selecteer het opgegeven veld onder **Lid toevoegen aan lijst** uit de parameterlijst of de lijst met dynamische inhoud: <p>' Mislukt ' lid niet toegevoegd aan ' test-leden-ML ': **E-mail adres**" | 
   | **Hoofdtekst** | <*hoofdtekst-voor-e-mail-bij-mislukken*> | De hoofdtekst voor het e-mailbericht bij mislukken. Voer voor deze zelfstudie deze tekst in: <p>‘Lid bestaat mogelijk al. Controleer uw MailChimp-account.’ | 
   | | | | 

5. Sla uw logische app op. 

Test vervolgens uw logische app, die er nu bijna net zo uitziet als dit voorbeeld:

 ![Voltooide logische app](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-complete.png)

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

1. Stuur uzelf een e-mailbericht met een aanvraag om lid te worden van uw adressenlijst.
Wacht totdat de aanvraag in uw Postvak IN aankomt.

3. Kies **Uitvoeren** op de werkbalk in de ontwerper als u de logische app handmatig wilt uitvoeren. 

   Als uw e-mail een onderwerp heeft dat overeenkomt met het onderwerpfilter van de trigger, stuurt uw logische app u een e-mail om de abonnementaanvraag goed te keuren.

4. Kies **Goedkeuren** in de goedkeurings-e-mail.

5. Als het e-mailadres van de abonnee niet voorkomt in uw adressenlijst, voegt uw logische app het e-mailadres van die persoon toe en stuurt u een e-mail zoals dit voorbeeld:

   ![E-mailbericht bij succes](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-success.png)

   Als uw logische app de abonnee niet kan toevoegen, krijgt u een e-mail als in dit voorbeeld:

   ![E-mail bij mislukken](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-failed.png)

   Als u geen een e-mailberichten ontvangt, controleert u de map met ongewenste e-mail. 
   Het is mogelijk dat uw filter voor ongewenste e-mail dit soort e-mails in deze map zet. 
   Als u niet zeker weet of uw logische app correct wordt uitgevoerd, kunt u [Problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md) raadplegen.

Gefeliciteerd, u hebt nu een logische app gemaakt en uitgevoerd die informatie in Azure, Microsoft-services en andere SaaS-apps integreert.

## <a name="clean-up-resources"></a>Resources opschonen

Als u die niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat. Ga in het Azure-hoofdmenu naar **Resourcegroepen** en selecteer de resourcegroep voor uw logische app. Kies **Resourcegroep verwijderen**. Voer ter bevestiging de naam van de resourcegroep in en kies **Verwijderen**.

!["Overzicht" > "Resourcegroep verwijderen"](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een logische app gemaakt die goedkeuringen voor adressenlijstaanvragen beheert. Leer nu hoe u een logische app kunt bouwen die e-mailbijlagen verwerkt en opslaat door integratie van Azure-diensten, zoals Azure Storage en Azure Functions.

> [!div class="nextstepaction"]
> [E-mailbijlagen verwerken](../logic-apps/tutorial-process-email-attachments-workflow.md)
