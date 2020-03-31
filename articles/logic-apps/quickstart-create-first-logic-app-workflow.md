---
title: Uw eerste geautomatiseerde workflow maken
description: Quickstart - Bouw uw eerste geautomatiseerde workflow met Behulp van Azure Logic Apps voor eAI-oplossingen (System Logic Apps) voor systeemintegratie en enterprise application integration (EAI)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: c2a26f2f40e0312fbfa0962e69ddfd0cfdabad5f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77191284"
---
# <a name="quickstart-create-your-first-workflow-by-using-azure-logic-apps---azure-portal"></a>Snelstart: uw eerste werkstroom maken met Azure Logic Apps - Azure-portal

Met deze quickstart worden de algemene basisconcepten geïntroduceerd voor het bouwen van uw eerste werkstroom met behulp van [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)zoals het maken van een lege logische app, het toevoegen van een trigger en een actie en het testen van uw logische app. In deze quickstart bouwt u een logische app die regelmatig de RSS-feed van een website controleert op nieuwe items. Als er nieuwe items bestaan, stuurt de logische app een e-mail voor elk item. Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Voorbeeldlogica-app-werkstroom op hoog niveau](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Voor dit scenario hebt u een e-mailaccount nodig van een service die wordt ondersteund door Azure Logic Apps, zoals Office 365 Outlook, Outlook.com of Gmail. Voor andere ondersteunde e-mailservices [bekijkt u hier de lijst met connectors.](https://docs.microsoft.com/connectors/) In dit voorbeeld gebruikt de logische app Office 365 Outlook. Als u een andere e-mailservice gebruikt, zijn de algemene stappen hetzelfde, maar kan uw gebruikersinterface enigszins verschillen.

Als u nog geen abonnement op Azure hebt, [meld u dan nu aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Zoek en selecteer op de startpagina van Azure in het zoekvak **Logische apps**.

   ![Zoeken en selecteren "Logische apps"](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Selecteer **op** de pagina Logische apps de optie **Toevoegen**.

   ![Nieuwe logische app toevoegen](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. Geef in het deelvenster **Logic App** details over uw logische app zoals hieronder weergegeven. Nadat u klaar bent, selecteert u **Maken**.

   ![Details geven voor de nieuwe logische app](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*logische-app-naam*> | De naam van uw logische app, die alleen`-`letters, cijfers, koppeltekens`(`( `)`), onderstreept`.`(`_`), haakjes ( , en perioden ( ). In dit voorbeeld wordt gebruik gemaakt van "My-First-Logic-App". |
   | **Abonnement** | <*Azure-abonnementsnaam*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | <*Naam azure-resource-groep*> | De naam voor de [Azure-brongroep](../azure-resource-manager/management/overview.md) die wordt gebruikt om gerelateerde resources te ordenen. In dit voorbeeld wordt "My-First-LA-RG" gebruikt. |
   | **Locatie** | <*Azure-regio*> | De regio waar u uw logica-app-informatie opslaan. In dit voorbeeld wordt "West US" gebruikt. |
   | **Logboekanalyse** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u op de werkbalk Azure de optie **Meldingen** > **Ga naar bron** voor uw geïmplementeerde logica-app.

   ![Ga naar nieuw gemaakte logic app-bron](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   U ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps-ontwerpfunctie wordt geopend en u ziet een pagina met een inleidende video en veelgebruikte triggers. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Lege sjabloon selecteren voor logische app](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Vervolgens voegt u een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die wordt geactiveerd zodra er een nieuw RSS-feeditem wordt weergegeven. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Elke keer dat de trigger wordt geactiveerd, maakt de Azure Logic Apps-engine een logische app-instantie die uw werkstroom start en uitvoert.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>De RSS-trigger toevoegen

1. Selecteer in de **Logic App Designer**onder het zoekvak de optie **Alles**.

1. Voer in het `rss` zoekvak in om de RSS-connector te zoeken. Selecteer in de lijst triggers de **trigger Wanneer een feeditem wordt gepubliceerd.**

   ![De trigger 'Wanneer een feeditem wordt gepubliceerd' selecteren](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Geef deze informatie voor uw trigger zoals hier weergegeven en beschreven:

   ![Trigger instellen met RSS-feed, frequentie en interval](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **De URL voor de RSS-feed** | `http://feeds.reuters.com/reuters/topNews` | De koppeling voor de RSS-feed die u wilt bijhouden |
   | **Interval** | 1 | Het aantal intervallen dat tussen controles moet worden gewacht |
   | **Frequentie** | Minuut | De tijdseenheid voor elk interval tussen controles  |
   ||||

   Het interval en de frequentie bepalen samen de planning van de trigger voor uw logische app. Deze logische app controleert de feed elke minuut.

1. Als u de triggerdetails voor nu wilt samenvouwen, klikt u in de titelbalk van de trigger.

   ![Logica-app-vorm samenvouwen om details te verbergen](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

Uw logische app is nu live, maar kan alleen de RSS-feed controleren. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="add-the-send-email-action"></a>De actie 'e-mail verzenden' toevoegen

Voeg nu een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die een e-mail verzendt wanneer een nieuw item wordt weergegeven in de RSS-feed.

1. Selecteer Onder de **trigger Wanneer een feeditem wordt gepubliceerd** de optie Nieuwe **stap**.

   ![Selecteer onder trigger de optie 'Nieuwe stap'](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Selecteer **onder Een actie kiezen** en het zoekvak de optie **Alles**.

1. Voer in het `send an email` zoekvak in om connectors te zoeken die deze actie aanbieden. Selecteer in de lijst met acties de actie 'een e-mail verzenden' voor de e-mailservice die u wilt gebruiken. In dit voorbeeld wordt de Office 365 Outlook-connector gebruikt, waarmee u **een e-mailactie verzenden** heeft.

   ![Selecteer de actie 'Een e-mail verzenden' voor Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Als u de lijst met acties wilt filteren op een bepaalde app of service, kunt u eerst die app of service selecteren:

   * Voor werk- of schoolaccounts van Azure selecteert u Outlook van Office 365.
   * Selecteer Outlook.com voor persoonlijke Microsoft-accounts.

1. Als de geselecteerde e-mailconnector u vraagt uw identiteit te verifiëren, voert u deze stap nu uit om een verbinding te maken tussen uw logische app en uw e-mailservice.

   > [!NOTE]
   > In dit specifieke voorbeeld verifieert u handmatig uw identiteit. Connectors waarvoor verificatie vereist is, verschillen echter in de verificatietypen die ze ondersteunen. U hebt ook opties om de manier in te stellen waarop u verificatie wilt verwerken. Wanneer u bijvoorbeeld Azure Resource Manager-sjablonen gebruikt voor implementatie, u de beveiliging van invoer die u vaak of eenvoudig wilt wijzigen, zoals verbindingsgegevens, parameteriseren en verbeteren. Zie deze onderwerpen voor meer informatie:
   >
   > * [Sjabloonparameters voor implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [OAuth-verbindingen autoriseren](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Toegang verifiëren met beheerde identiteiten](../logic-apps/create-managed-service-identity.md)
   > * [Verbindingen verifiëren voor implementatie van logische apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. In de actie **Een e-mail verzenden** geeft u de gegevens op die u in het e-mailbericht wilt opnemen.

   1. Voer het e-mailadres van de ontvanger in het vak **Aan** in. Voor testdoeleinden u uw e-mailadres gebruiken.

      Negeer voorlopig de lijst **Dynamische inhoud toevoegen** die wordt weergegeven. Wanneer u in bepaalde invoervakken klikt, wordt deze lijst geopend met alle beschikbare parameters uit de vorige stap die u als invoer in uw werkstroom kunt opnemen.

   1. Voer in het vak **Onderwerp** deze tekst met een afsluitende spatie in: `New RSS item: `

      ![Voer in de eigenschap Onderwerp uw e-mailonderwerp in](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. In de lijst **Dynamische inhoud toevoegen** selecteert u **Feedtitel** om de titel van het RSS-item op te nemen.

      ![Selecteer in de lijst met dynamische inhoud de eigenschap 'Feedtitel', eigenschap](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Wanneer u klaar bent, ziet het onderwerp van de e-mail eruit zoals in dit voorbeeld:

      ![Voorbeeld van voltooid e-mailonderwerp voor toegevoegde feedtitel](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Als een 'For each'-lus in de ontwerpfunctie wordt weergegeven, hebt u een token voor een matrix geselecteerd, bijvoorbeeld het token **categories-Item**. Voor dit type tokens voegt de ontwerpfunctie automatisch deze lus toe rond de actie die verwijst naar dat token. Op die manier wordt dezelfde actie uitgevoerd voor elk matrixitem. Als u de lus wilt verwijderen, selecteert u de **ellips** (**...**) op de titelbalk van de lus en selecteert u **Verwijderen**.

   1. In het vak voor de **hoofdtekst** voert u deze tekst in en selecteert u deze tokens voor de inhoud van het e-mailbericht. Als u lege regels wilt toevoegen in een invoervak, drukt u op Shift + Enter.

      ![Eigenschappen selecteren voor inhoud van e-mailbody](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Eigenschap | Beschrijving |
      |----------|-------------|
      | **Feedtitel** | De titel van het item |
      | **Feed gepubliceerd op** | De publicatiedatum en -tijd van het item |
      | **Primaire feedkoppeling** | De URL voor het item |
      |||

1. Sla uw logische app op.

Test vervolgens de logische app.

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

Als u uw logische app handmatig wilt starten, selecteert u op de werkbalkbalk van de ontwerper de optie **Uitvoeren**. Of wacht tot de logische app de RSS-feed op basis van uw opgegeven schema (elke minuut) heeft gecontroleerd. Als de RSS-feed nieuwe items heeft, verzendt uw logische app een e-mailbericht voor elk nieuw item. Anders wacht de logische app tot het volgende interval voordat opnieuw een controle wordt uitgevoerd. Als u geen een e-mailberichten ontvangt, controleert u de map Ongewenste e-mail.

Dit is een voorbeeld van een e-mailbericht dat deze logische app verzendt.

![Voorbeelde-e-mail verzonden wanneer nieuw RSS-feeditem wordt weergegeven](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Wanneer de trigger de RSS-feed controleert en nieuwe items vindt, worden de triggerbranden uitgevoerd en maakt de Azure Logic Apps-engine een instantie van uw logische app-werkstroom waarmee de acties in de werkstroom worden uitgevoerd. Als de trigger geen nieuwe items vindt, wordt deze niet geactiveerd en wordt het instantiëren van de werkstroom overgeslagen.

Gefeliciteerd, u hebt nu met succes uw eerste logische app met de Azure-portal gebouwd en uitgevoerd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u dit voorbeeld niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat.

1. Selecteer **resourcegroepen**in het hoofdmenu van Azure en selecteer vervolgens de brongroep van uw logische app. Selecteer **resourcegroep** **verwijderen**in het deelvenster Overzicht .

   ![Brongroep zoeken, selecteren en verwijderen](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Wanneer het bevestigingsvenster wordt weergegeven, voert u de naam van de brongroep in en selecteert u **Verwijderen**.

   ![Als u verwijdering wilt bevestigen, selecteert u 'Verwijderen'.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Wanneer u een logische app verwijdert, worden geen nieuwe uitvoeringen gemaakt. Alle uitvoeringen die bezig zijn en wachten op uitvoering worden geannuleerd. Als u duizenden uitvoeringen hebt, kan de annulering een aanzienlijke tijd in beslag nemen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u uw eerste logische app gemaakt die controleert op de aanwezigheid van RSS-updates op basis van de opgegeven planning (elke minuut) en onderneemt actie (stuurt e-mail) wanneer er updates zijn. Als u meer wilt weten, gaat u verder met deze zelfstudie om geavanceerdere werkstromen op basis van een planning te maken:

> [!div class="nextstepaction"]
> [Verkeer controleren met een logische app op basis van een planning](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
