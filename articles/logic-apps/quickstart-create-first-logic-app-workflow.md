---
title: Uw eerste geautomatiseerde werkstroom maken
description: 'Quickstart: Uw eerste geautomatiseerde werkstroom bouwen met behulp van Azure Logic Apps voor systeemintegratie en EAI-oplossingen (Enterprise Application Integration)'
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: 0b3559a27fe9fae6c34b07c648a289d205560bd8
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85321584"
---
# <a name="quickstart-create-your-first-workflow-by-using-azure-logic-apps---azure-portal"></a>Quickstart: Uw eerste werkstroom maken met Azure Logic Apps - Azure Portal

In deze quickstart worden de basisconcepten geïntroduceerd voor het maken van uw eerste werkstroom met behulp van [Azure Logic Apps](../logic-apps/logic-apps-overview.md), zoals het maken van een lege logische app, het toevoegen van een trigger en een actie, en het testen van uw logische app. In deze quickstart maakt u een logische app waarmee de RSS-feed van een website regelmatig wordt gecontroleerd op nieuwe items. Als er nieuwe items bestaan, stuurt de logische app een e-mail voor elk item. Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Voorbeeld van een logische app-werkstroom op hoog niveau](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Voor dit scenario hebt u een Azure-abonnement of [een gratis Azure-account](https://azure.microsoft.com/free/) nodig, en een e-mailaccount van een service die wordt ondersteund door Azure Logic Apps, zoals Office 365 Outlook, Outlook.com of Gmail. [Bekijk hier de lijst met connectors](https://docs.microsoft.com/connectors/) voor andere ondersteunde e-mailservices. In dit voorbeeld maakt de logische app gebruik van een Office 365 Outlook-account. Als u een andere e-mailservice gebruikt, zijn de algemene stappen hetzelfde, maar kan uw gebruikersinterface enigszins verschillen.

> [!IMPORTANT]
> Als u de Gmail-connector wilt gebruiken, kunnen alleen bedrijfsaccounts van G Suite deze connector zonder beperking in logische apps gebruiken. Als u een Gmail-consumentenaccount hebt, kunt u deze connector alleen gebruiken met specifieke door Google goedgekeurde services, of u kunt [een Google-client-app maken voor verificatie bij uw Gmail-connector](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Zie [Beleid voor gegevensbeveiliging en privacybeleid voor Google-connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md) voor meer informatie.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Zoek in het zoekvak op de startpagina van Azure de functie **Logic Apps**.

   ![Zoek en selecteer 'Logic Apps'](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Selecteer op de pagina **Logic Apps** de optie **Toevoegen**.

   ![Nieuwe logische app toevoegen](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. Voer in het deelvenster **Logische app** informatie in over uw logische app (zie hieronder). Selecteer **Maken** als u klaar bent.

   ![Geef details op voor de nieuwe logische app](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*naam-van-logische-app*> | De naam van uw logische app; deze kan alleen letters, cijfers, afbreekstreepjes (`-`), onderstrepingstekens (`_`), haakjes (`(`, `)`) en punten (`.`) bevatten. In dit voorbeeld wordt 'My-First-Logic-App' gebruikt. <p><p>**Opmerking**: Namen voor logische apps moeten uniek zijn in verschillende regio's. |
   | **Abonnement** | <*Azure-subscription-name*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | <*Naam-van-Azure-resourcegroep*> | De naam van de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) die wordt gebruikt om verwante resources te organiseren. In dit voor beeld wordt 'My-First-LA-RG' gebruikt. <p><p>**Opmerking**: Namen voor resourcegroepen moeten uniek zijn in verschillende regio's. |
   | **Locatie** | <*Azure-regio*> | De regio waarin informatie over uw logische app moet worden opgeslagen. In dit voorbeeld wordt 'US - west' gebruikt. |
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u in de Azure-werkbalk de optie **Meldingen** > **Resources openen** voor uw geïmplementeerde logische app.

   ![De zojuist gemaakte logische app-resource openen](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   U kunt ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps-ontwerpfunctie wordt geopend en u ziet een pagina met een inleidende video en veelgebruikte triggers. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Lege sjabloon voor de logische app selecteren](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Vervolgens voegt u een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die wordt geactiveerd zodra er een nieuw RSS-feeditem wordt weergegeven. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Steeds wanneer de trigger wordt geactiveerd, maakt de Azure Logic Apps-engine een exemplaar van een logische app dat wordt gestart en de werkstroom uitvoert.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>De RSS-trigger toevoegen

1. Selecteer in de **Ontwerpfunctie van logische app** onder het zoekvak de optie **Alle**.

1. Voer in het zoekvak `rss` in om de RSS-connector te vinden. Selecteer in de lijst triggers de trigger **Wanneer een feeditem wordt gepubliceerd**.

   ![De trigger 'Wanneer een feeditem wordt gepubliceerd' selecteren](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Geef de informatie voor de trigger op zoals beschreven in de tabel:

   ![Trigger instellen met RSS-feed, frequentie en interval](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **De URL voor de RSS-feed** | <*RSS-feed-URL*> | De koppeling voor de RSS-feed die u wilt bijhouden. In dit voorbeeld wordt `http://feeds.reuters.com/reuters/topNews` gebruikt. |
   | **Interval** | 1 | Het aantal intervallen dat tussen controles moet worden gewacht |
   | **Frequentie** | Minuut | De tijdseenheid voor elk interval tussen controles  |
   ||||

   Het interval en de frequentie bepalen samen de planning van de trigger voor uw logische app. Deze logische app controleert de feed elke minuut.

1. Als u de details van de trigger voorlopig wilt samenvouwen, klikt u in de titelbalk van de trigger.

   ![Vorm van logische app samenvouwen om details te verbergen](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Uw logische app is nu live, maar kan alleen de RSS-feed controleren. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="add-the-send-email-action"></a>De actie 'E-mail verzenden' toevoegen

Voeg nu een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe waardoor een e-mail wordt verzonden zodra een nieuw item in de RSS-feed wordt weergegeven.

1. Selecteer onder de trigger **Wanneer een feeditem wordt gepubliceerd** **Nieuwe stap**.

   ![Selecteer onder trigger 'Nieuwe stap'](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Selecteer onder **Kies een actie** en het zoekvak de optie **Alle**.

1. Voer in het zoekvak `send an email` in om connectors te vinden die deze actie aanbieden. Selecteer in de lijst met acties de actie 'een e-mail verzenden' voor de e-mailservice die u wilt gebruiken. In dit voorbeeld wordt de Office 365 Outlook-connector gebruikt. Deze heeft de actie **Een e-mail verzenden**.

   ![Selecteer de actie 'Een e-mail verzenden' voor Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Als u de lijst met acties wilt filteren op een bepaalde app of service, kunt u eerst die app of service selecteren:

   * Voor werk- of schoolaccounts van Azure selecteert u Outlook van Office 365.
   * Selecteer Outlook.com voor persoonlijke Microsoft-accounts.

1. Als uw geselecteerde e-mailconnector u vraagt om uw identiteit te verifiëren, moet u die stap nu uitvoeren om een verbinding tot stand te brengen tussen uw logische app en uw e-mailservice.

   > [!NOTE]
   > In dit specifieke voorbeeld moet u uw identiteit handmatig verifiëren. Connectors waarvoor verificatie is vereist, verschillen echter in de verificatietypen die ze ondersteunen. U hebt ook opties om de manier in te stellen waarop u de verificatie wilt afhandelen. Wanneer u bijvoorbeeld Azure Resource Manager-sjablonen gebruikt voor implementatie, kunt u parameters en de beveiliging verbeteren voor invoer die u vaak of eenvoudig wilt wijzigen, zoals verbindingsgegevens. Raadpleeg de volgende onderwerpen voor meer informatie:
   >
   > * [Sjabloonparameters voor implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [OAuth-verbindingen autoriseren](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Toegang verifiëren met beheerde identiteiten](../logic-apps/create-managed-service-identity.md)
   > * [Verbindingen voor implementatie van logische apps verifiëren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. In de actie **Een e-mail verzenden** geeft u de gegevens op die u in het e-mailbericht wilt opnemen.

   1. Voer het e-mailadres van de ontvanger in het vak **Aan** in. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken.

      Negeer voorlopig de lijst **Dynamische inhoud toevoegen** die wordt weergegeven. Wanneer u in bepaalde invoervakken klikt, wordt deze lijst geopend met alle beschikbare parameters uit de vorige stap die u als invoer in uw werkstroom kunt opnemen.

   1. Voer in het vak **Onderwerp** deze tekst met een afsluitende spatie in: `New RSS item: `

      ![Voer bij de eigenschap 'Onderwerp' het onderwerp van uw e-mail in](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. In de lijst **Dynamische inhoud toevoegen** selecteert u **Feedtitel** om de titel van het RSS-item op te nemen.

      ![Selecteer in de lijst met dynamische inhoud de eigenschap 'Feedtitel'](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Wanneer u klaar bent, ziet het onderwerp van de e-mail eruit zoals in dit voorbeeld:

      ![Voorbeeld van een e-mailonderwerp voor de toegevoegde feedtitel](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Als een 'For each'-lus in de ontwerpfunctie wordt weergegeven, hebt u een token voor een matrix geselecteerd, bijvoorbeeld het token **categories-Item**. Voor dit type tokens voegt de ontwerpfunctie automatisch deze lus toe rond de actie die verwijst naar dat token. Op die manier wordt dezelfde actie uitgevoerd voor elk matrixitem. Selecteer het **beletselteken** ( **...** ) op de titelbalk van de lus en kies vervolgens **Verwijderen** om de lus te verwijderen.

   1. In het vak voor de **hoofdtekst** voert u deze tekst in en selecteert u deze tokens voor de inhoud van het e-mailbericht. Als u lege regels wilt toevoegen in een invoervak, drukt u op Shift + Enter.

      ![Eigenschappen voor inhoud van het e-mailbericht selecteren](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Eigenschap | Beschrijving |
      |----------|-------------|
      | **Feedtitel** | De titel van het item |
      | **Feed gepubliceerd op** | De publicatiedatum en -tijd van het item |
      | **Primaire feedkoppeling** | De URL voor het item |
      |||

1. Sla uw logische app op.

Test vervolgens de logische app.

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

Selecteer **Uitvoeren** op de werkbalk in de ontwerper als u de logische app handmatig wilt uitvoeren. Of wacht tot de logische app de RSS-feed op basis van uw opgegeven schema (elke minuut) heeft gecontroleerd. Als de RSS-feed nieuwe items heeft, verzendt uw logische app een e-mailbericht voor elk nieuw item. Anders wacht de logische app tot het volgende interval voordat opnieuw een controle wordt uitgevoerd. Als u geen een e-mailberichten ontvangt, controleert u de map Ongewenste e-mail.

Dit is een voorbeeld van een e-mailbericht dat deze logische app verzendt.

![Voorbeeldbericht dat wordt verzonden wanneer het nieuwe RSS-feeditem wordt weergegeven](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technisch gezien wordt, als bij controle van de RSS-feed nieuwe items worden aangetroffen, de trigger geactiveerd en maakt de Azure Logic Apps-engine een exemplaar van de werkstroom van uw logische app die de acties in de werkstroom uitvoert. Als de trigger geen nieuwe items vindt, wordt deze niet geactiveerd en wordt het instantiëren van de werkstroom overgeslagen.

U hebt nu uw eerste logische app in Azure Portal gemaakt en uitgevoerd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u dit voorbeeld niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat.

1. Selecteer in het Azure-hoofdmenu de optie **Resourcegroepen** en selecteer vervolgens de resourcegroep voor uw logische app. Selecteer op de pagina **Overzicht** de optie **Resourcegroep verwijderen**.

   ![Resourcegroep vinden, selecteren en verwijderen](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Voer de naam van de resourcegroep in en selecteer **Verwijderen** als het bevestigingsdeelvenster wordt weergegeven.

   ![Selecteer 'Verwijderen' om het verwijderen te bevestigen](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Wanneer u een logische app verwijdert, worden geen nieuwe uitvoeringen gemaakt. Alle uitvoeringen die bezig zijn en wachten op uitvoering worden geannuleerd. Als u duizenden uitvoeringen hebt, kan de annulering een aanzienlijke tijd in beslag nemen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u uw eerste logische app gemaakt die controleert op de aanwezigheid van RSS-updates op basis van de opgegeven planning (elke minuut) en onderneemt actie (stuurt e-mail) wanneer er updates zijn. Als u meer wilt weten, gaat u verder met deze zelfstudie om geavanceerdere werkstromen op basis van een planning te maken:

> [!div class="nextstepaction"]
> [Verkeer controleren met een logische app op basis van een planning](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
