---
title: Een geautomatiseerde integratiewerkstroom maken
description: 'Quickstart: Uw eerste geautomatiseerde werkstroom bouwen met behulp van Azure Logic Apps voor systeemintegratie en EAI-oplossingen (Enterprise Application Integration)'
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/23/2020
ms.openlocfilehash: 980e3e036257bbf5aa9743025bbfb55065176a39
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133296"
---
# <a name="quickstart-create-your-first-automated-integration-workflow-by-using-azure-logic-apps---azure-portal"></a>Quickstart: Uw eerste geautomatiseerde integratiewerkstroom maken met Azure Logic Apps - Azure-portal

In deze quickstart worden de basisconcepten geïntroduceerd voor het maken van uw eerste werkstroom met behulp van [Azure Logic Apps](logic-apps-overview.md), zoals het maken van een lege logische app, het toevoegen van een trigger en een actie, en het testen van uw logische app. In deze quickstart maakt u een logische app waarmee de RSS-feed van een website regelmatig wordt gecontroleerd op nieuwe items. Als er nieuwe items bestaan, stuurt de logische app een e-mail voor elk item. Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Conceptuele afbeelding met algemeen voorbeeld van een werkstroom een voor logische app.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Voor dit scenario hebt u een Azure-abonnement of [een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nodig, en een e-mailaccount van een service die wordt ondersteund door Azure Logic Apps, zoals Office 365 Outlook, Outlook.com of Gmail. [Bekijk hier de lijst met connectors](/connectors/) voor andere ondersteunde e-mailservices. In dit voorbeeld maakt de logische app gebruik van een Office 365 Outlook-account. Als u een andere e-mailservice gebruikt, zijn de algemene stappen hetzelfde, maar kan uw gebruikersinterface enigszins verschillen.

> [!IMPORTANT]
> Als u de Gmail-connector wilt gebruiken, kunnen alleen bedrijfsaccounts van G Suite deze connector zonder beperking in logische apps gebruiken. Als u een Gmail-consumentenaccount hebt, kunt u deze connector alleen gebruiken met specifieke door Google goedgekeurde services, of u kunt [een Google-client-app maken voor verificatie bij uw Gmail-connector](/connectors/gmail/#authentication-and-bring-your-own-application). Zie [Beleid voor gegevensbeveiliging en privacybeleid voor Google-connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md) voor meer informatie.

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Voer in het zoek van de Azure-portal `logic apps` in en selecteer **Logic Apps**.

   ![Schermopname van het zoekvak in de Azure-portal met 'logic apps' als zoekterm en 'Logic Apps' als het geselecteerde zoekresultaat.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Selecteer op de pagina **Logic Apps** de optie **Toevoegen**.

   ![Schermopname van de lijst met logische apps en de geselecteerde knop 'Toevoegen'.](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. Voer in het deelvenster **Logische app** informatie in over uw logische app (zie hieronder).

   ![Schermopname van het deelvenster voor het maken van logische apps met details voor een nieuwe logische app.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*naam-van-logische-app*> | De naam van uw logische app. Deze moet uniek zijn binnen alle regio's en mag alleen letters, cijfers, afbreekstreepjes (`-`), onderstrepingstekens (`_`), haakjes (`(`, `)`) en punten (`.`) bevatten. In dit voorbeeld wordt 'My-First-Logic-App' gebruikt. |
   | **Abonnement** | <*Azure-subscription-name*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | <*Naam-van-Azure-resourcegroep*> | De naam voor de [Azure-resourcegroep](../azure-resource-manager/management/overview.md). Deze moet uniek zijn binnen alle regio's en wordt gebruikt om gerelateerde resources te ordenen. In dit voor beeld wordt 'My-First-LA-RG' gebruikt. |
   | **Locatie** | <*Azure-regio*> | De regio waarin informatie over uw logische app moet worden opgeslagen. In dit voorbeeld wordt 'US - west' gebruikt. |
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Als u klaar bent, selecteert u **Beoordelen en maken**. Controleer de gegevens die u hebt ingevoerd en selecteer **Maken**.

1. Nadat uw app in Azure is geïmplementeerd, selecteert u **Naar de resource gaan**.

   ![Schermopname van de pagina voor het implementeren van een resource en de geselecteerde knop om naar de resource te gaan.](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   U kunt ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps-ontwerpfunctie wordt geopend en u ziet een pagina met een inleidende video en veelgebruikte triggers. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Schermopname van de sjabloongalerie van Logic Apps Designer en de geselecteerde sjabloon 'Lege logische app'.](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Vervolgens voegt u een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die wordt geactiveerd zodra er een nieuw RSS-feeditem wordt weergegeven. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Steeds wanneer de trigger wordt geactiveerd, maakt de Azure Logic Apps-engine een exemplaar van een logische app dat wordt gestart en de werkstroom uitvoert.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>De RSS-trigger toevoegen

1. Selecteer in de **Ontwerpfunctie van logische app** onder het zoekvak de optie **Alle**.

1. Typ `rss` in het zoekvak om de RSS-connector te vinden. Selecteer in de lijst met triggers de RSS-trigger **Wanneer een feeditem wordt gepubliceerd**.

   ![Schermopname van Logic Apps Designer met 'rss' in het zoekvak en de geselecteerde trigger 'Wanneer een feeditem wordt gepubliceerd'.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Geef de informatie voor de trigger op zoals beschreven in deze stap:

   ![Schermopname van Logic Apps Designer met instellingen voor een RSS-trigger, inclusief de URL, de frequentie en het interval.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **De URL voor de RSS-feed** | <*RSS-feed-URL*> | De koppeling voor de RSS-feed die u wilt bijhouden. In dit voorbeeld wordt de RSS-feed van de Wall Street Journal op `https://feeds.a.dj.com/rss/RSSMarketsMain.xml` gebruikt, maar desgewenst kunt u de URL van uw eigen RSS-feed gebruiken. |
   | **Interval** | 1 | Het aantal intervallen dat tussen controles moet worden gewacht |
   | **Frequentie** | Minuut | De tijdseenheid voor elk interval tussen controles |
   ||||

   Het interval en de frequentie bepalen samen de planning van de trigger voor uw logische app. Deze logische app controleert de feed elke minuut.

1. Als u de details van de trigger voorlopig wilt samenvouwen, klikt u in de titelbalk van de trigger.

   ![Schermopname van Logic Apps Designer met een samengevouwen shape van een logische app.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Uw logische app is nu live, maar kan alleen de RSS-feed controleren. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="add-the-send-email-action"></a>De actie 'E-mail verzenden' toevoegen

Voeg nu een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe waardoor een e-mail wordt verzonden zodra een nieuw item in de RSS-feed wordt weergegeven.

1. Selecteer onder de trigger **Wanneer een feeditem wordt gepubliceerd** **Nieuwe stap**.

   ![Schermopname van Logic Apps Designer met 'Nieuwe stap'.](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Selecteer onder **Kies een actie** en het zoekvak de optie **Alle**.

1. Typ `send an email` in het zoekvak om connectors te vinden die deze actie aanbieden. Als u de lijst met acties wilt filteren op een bepaalde app of service, kunt u eerst die app of service selecteren.

   Als u bijvoorbeeld een werk- of schoolaccount van Microsoft gebruikt en Office 365 Outlook wilt gebruiken, selecteert u **Office 365 Outlook**. Als u gebruikmaakt van een persoonlijk Microsoft-account, kunt u ook Outlook.com selecteren. In dit voorbeeld wordt verder Office 365 Outlook gebruikt:

   ![Schermopname van Logic Apps Designer en geselecteerde Office 365 Outlook-connector.](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   U kunt de actie die u wilt gebruiken nu gemakkelijker vinden en selecteren, bijvoorbeeld `send an email`:

   ![Schermopname van Logic Apps Designer en lijst met gefilterde acties.](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Als uw geselecteerde e-mailconnector u vraagt om uw identiteit te verifiëren, moet u die stap nu uitvoeren om een verbinding tot stand te brengen tussen uw logische app en uw e-mailservice.

   > [!NOTE]
   > In dit specifieke voorbeeld moet u uw identiteit handmatig verifiëren. Connectors waarvoor verificatie is vereist, verschillen echter in de verificatietypen die ze ondersteunen. U hebt ook opties om de manier in te stellen waarop u de verificatie wilt afhandelen. Wanneer u bijvoorbeeld Azure Resource Manager-sjablonen gebruikt voor implementatie, kunt u parameters en de beveiliging verbeteren voor invoer die u vaak of eenvoudig wilt wijzigen, zoals verbindingsgegevens. Raadpleeg de volgende onderwerpen voor meer informatie:
   >
   > * [Sjabloonparameters voor implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [OAuth-verbindingen autoriseren](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Toegang verifiëren met beheerde identiteiten](../logic-apps/create-managed-service-identity.md)
   > * [Verbindingen voor implementatie van logische apps verifiëren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. In de actie **Een e-mail verzenden** geeft u de informatie op die u in het e-mailbericht wilt opnemen.

   1. Voer het e-mailadres van de ontvanger in het vak **Aan** in. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken.

      Negeer voorlopig de lijst **Dynamische inhoud toevoegen** die wordt weergegeven. Wanneer u in bepaalde invoervakken klikt, wordt deze lijst geopend met alle beschikbare uitvoer uit de vorige stap die u kunt gebruiken als invoer voor de huidige actie.

   1. Voer in het vak **Onderwerp** deze tekst met een afsluitende spatie in: `New RSS item: `

      ![Schermopname van Logic Apps Designer met de actie 'Een e-mail verzenden' en de cursor in het vak voor de eigenschap 'Onderwerp'.](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. Selecteer in de lijst **Dynamische inhoud toevoegen** **Feedtitel**, die wordt geleverd als uitvoer van de trigger 'Wanneer een feeditem wordt gepubliceerd' zodat de titel van het RSS-item beschikbaar wordt gemaakt voor gebruik.

      ![Schermopname van Logic Apps Designer met de actie 'Een e-mail verzenden' en de cursor in het vak voor de eigenschap 'Onderwerp' met een lijst met geopende dynamische inhoud en de geselecteerde uitvoer 'Feedtitel'.](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Als er in de lijst met dynamische inhoud geen uitvoer wordt weergegeven van de trigger 'Wanneer een feeditem wordt gepubliceerd' naast de header van de actie, selecteert u **Meer weergeven**.
      > 
      > ![Schermopname van Logic Apps Designer met de lijst met geopende dynamische inhoud en 'Meer weergeven' geselecteerd voor de trigger.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Wanneer u klaar bent, ziet het onderwerp van de e-mail eruit zoals in dit voorbeeld:

      ![Schermopname van Logic Apps Designer met de actie 'Een e-mail verzenden' en een voorbeeld van een e-mailonderwerp met opgenomen eigenschap 'Feedtitel'.](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Als een 'For each'-lus in de ontwerpfunctie wordt weergegeven, hebt u een token voor een matrix geselecteerd, bijvoorbeeld het token **categories-Item**. Voor dit type tokens voegt de ontwerpfunctie automatisch deze lus toe rond de actie die verwijst naar dat token. Op die manier wordt dezelfde actie uitgevoerd voor elk matrixitem. Selecteer het **beletselteken** ( **...** ) op de titelbalk van de lus en kies vervolgens **Verwijderen** om de lus te verwijderen.

   1. In het vak voor de **hoofdtekst** voert u deze tekst in en selecteert u deze tokens voor de inhoud van het e-mailbericht. Als u lege regels wilt toevoegen in een invoervak, drukt u op Shift + Enter.

      ![Schermopname van Logic Apps Designer met de actie 'Een e-mail verzenden' en geselecteerde eigenschappen in het vak 'Hoofdtekst'.](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

      | Eigenschap | Beschrijving |
      |----------|-------------|
      | **Feedtitel** | De titel van het item |
      | **Feed gepubliceerd op** | De publicatiedatum en -tijd van het item |
      | **Primaire feedkoppeling** | De URL voor het item |
      |||

1. Sla uw logische app op.

Test vervolgens de logische app.

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

Selecteer **Uitvoeren** op de werkbalk in de ontwerper als u de logische app handmatig wilt uitvoeren. Of wacht tot de logische app de RSS-feed op basis van uw opgegeven schema (elke minuut) heeft gecontroleerd.

![Schermopname van Logic Apps Designer met de knop 'Uitvoeren' geselecteerd op de werkbalk van de ontwerper.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Als de RSS-feed nieuwe items heeft, verzendt uw logische app een e-mailbericht voor elk nieuw item. Anders wacht de logische app tot het volgende interval voordat opnieuw een controle wordt uitgevoerd. Als u geen een e-mailberichten ontvangt, controleert u de map Ongewenste e-mail.

Dit is een voorbeeld van een e-mailbericht dat deze logische app verzendt.

![Schermopname van voorbeeld-e-mail die wordt ontvangen wanneer een nieuw RSS-feeditem wordt weergegeven.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technisch gezien wordt, als bij controle van de RSS-feed nieuwe items worden aangetroffen, de trigger geactiveerd en maakt de Azure Logic Apps-engine een exemplaar van de werkstroom van uw logische app die de acties in de werkstroom uitvoert. Als de trigger geen nieuwe items vindt, wordt deze niet geactiveerd en wordt het instantiëren van de werkstroom overgeslagen.

U hebt nu uw eerste logische app in Azure Portal gemaakt en uitgevoerd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u dit voorbeeld niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat.

1. Typ `resource groups` in het zoekvak van Azure en selecteer **Resourcegroepen**.

   ![Schermopname van het zoekvak van de Azure-portal zoekvak met de zoekterm 'resourcegroepen'.](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Zoek en selecteer de resourcegroep van uw logische app. Selecteer op de pagina **Overzicht** de optie **Resourcegroep verwijderen**.

   ![Schermopname van de Azure-portal met de geselecteerde resourcegroep en de knop 'Resourcegroep verwijderen'.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Voer de naam van de resourcegroep in en selecteer **Verwijderen** als het bevestigingsdeelvenster wordt weergegeven.

   ![Schermopname van de Azure-portal met het bevestigingsvenster en de ingevoerde naam van de resourcegroep die u wilt verwijderen.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Wanneer u een logische app verwijdert, worden geen nieuwe uitvoeringen gemaakt. Alle uitvoeringen die bezig zijn en wachten op uitvoering worden geannuleerd. Als u duizenden uitvoeringen hebt, kan de annulering een aanzienlijke tijd in beslag nemen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u uw eerste logische app gemaakt die controleert op de aanwezigheid van RSS-updates op basis van de opgegeven planning (elke minuut) en onderneemt actie (stuurt e-mail) wanneer er updates zijn. Als u meer wilt weten, gaat u verder met deze zelfstudie om geavanceerdere werkstromen op basis van een planning te maken:

> [!div class="nextstepaction"]
> [Verkeer controleren met een logische app op basis van een planning](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
