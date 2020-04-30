---
title: Uw eerste geautomatiseerde werk stroom maken
description: 'Snelstartgids: uw eerste geautomatiseerde werk stroom bouwen met behulp van Azure Logic Apps voor systeem integratie en EAI-oplossingen (Enter prise Application Integration)'
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: 3087b964ff5f9754d6552fc95625541ce94a6535
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82148003"
---
# <a name="quickstart-create-your-first-workflow-by-using-azure-logic-apps---azure-portal"></a>Snelstartgids: uw eerste werk stroom maken met behulp van Azure Logic Apps-Azure Portal

In deze Quick Start worden de basis concepten geïntroduceerd van het maken van uw eerste werk stroom met behulp van [Azure Logic apps](../logic-apps/logic-apps-overview.md), zoals het maken van een lege logische app, het toevoegen van een trigger en een actie, en het testen van uw logische app. In deze Quick Start maakt u een logische app waarmee de RSS-feed van een website regel matig wordt gecontroleerd op nieuwe items. Als er nieuwe items bestaan, stuurt de logische app een e-mail voor elk item. Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Voor beeld van een logische app-werk stroom op hoog niveau](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Voor dit scenario hebt u een Azure-abonnement nodig of moet u [zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/), een e-mail account van een service die wordt ondersteund door Azure Logic apps, zoals Office 365 Outlook, Outlook.com of Gmail. [Raadpleeg de lijst met connectors hier](https://docs.microsoft.com/connectors/)voor andere ondersteunde e-mail services. In dit voor beeld gebruikt de logische app een Office 365 Outlook-account. Als u een andere e-mail service gebruikt, zijn de algemene stappen hetzelfde, maar uw gebruikers interface kan enigszins verschillen.

> [!IMPORTANT]
> Als u de Gmail-connector wilt gebruiken, kunnen alleen zakelijke accounts van G-Suite deze connector gebruiken zonder beperkingen in Logic apps. Als u een Gmail-Consumer-account hebt, kunt u deze connector gebruiken met alleen specifieke door Google goedgekeurde Services, of u kunt [een Google-client-app maken die voor verificatie met uw Gmail-connector wordt gebruikt](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Zie voor meer informatie [beleid voor gegevens beveiliging en privacybeleid voor Google connectors in azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Ga naar de start pagina van Azure en selecteer **Logic apps**in het zoekvak.

   ![Zoek en selecteer ' Logic Apps '](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Selecteer op de pagina **Logic apps** **toevoegen**.

   ![Nieuwe logische app toevoegen](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. Geef in het deel venster **logische app** Details op over uw logische app, zoals hieronder wordt weer gegeven. Wanneer u klaar bent, selecteert u **maken**.

   ![Geef details op voor de nieuwe logische app](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*Logic-app-naam*> | De naam van de logische app, die alleen letters, cijfers, afbreek streepjes`-`(), onderstrepings tekens`_`(), haakjes`(`( `)`,) en punten (`.`) kan bevatten. In dit voor beeld wordt ' mijn-eerste logica-app ' gebruikt. |
   | **Abonnement** | <*Azure-abonnement-naam*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | <*Azure-resource-group-name*> | De naam voor de [Azure-resource groep](../azure-resource-manager/management/overview.md) die wordt gebruikt om verwante resources te organiseren. In dit voor beeld wordt ' My-First-LA-RG ' gebruikt. |
   | **Locatie** | <*Azure-regio*> | De regio waar u de gegevens van uw logische app opslaat. In dit voor beeld wordt ' West US ' gebruikt. |
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u in de Azure-werk balk de optie **meldingen** > **gaat u** naar de resource voor uw geïmplementeerde logische app.

   ![Ga naar de zojuist gemaakte logische app-resource](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   U kunt ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps-ontwerpfunctie wordt geopend en u ziet een pagina met een inleidende video en veelgebruikte triggers. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Lege sjabloon voor logische app selecteren](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Vervolgens voegt u een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die wordt geactiveerd zodra er een nieuw RSS-feeditem wordt weergegeven. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Azure Logic Apps-Engine een exemplaar van een logische app waarmee de werk stroom wordt gestart en uitgevoerd.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>De RSS-trigger toevoegen

1. Selecteer in de **ontwerp functie voor logische apps**, onder het zoekvak, de optie **alle**.

1. Typ `rss` in het zoekvak om de RSS-connector te vinden. Selecteer in de lijst triggers de trigger **Wanneer een feed-item wordt gepubliceerd** .

   ![De trigger ' wanneer een feed-item wordt gepubliceerd ' selecteren](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Geef deze informatie voor de trigger op zoals hier wordt weer gegeven en beschreven:

   ![Trigger instellen met RSS-feed, frequentie en interval](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **De URL voor de RSS-feed** | `http://feeds.reuters.com/reuters/topNews` | De koppeling voor de RSS-feed die u wilt bijhouden |
   | **Bereik** | 1 | Het aantal intervallen dat tussen controles moet worden gewacht |
   | **Frequentie** | Minuut | De tijdseenheid voor elk interval tussen controles  |
   ||||

   Het interval en de frequentie bepalen samen de planning van de trigger voor uw logische app. Deze logische app controleert de feed elke minuut.

1. Als u de details van de trigger voorlopig wilt samen vouwen, klikt u in de titel balk van de trigger.

   ![De shape Logic app samen vouwen om details te verbergen](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

Uw logische app is nu live, maar kan alleen de RSS-feed controleren. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="add-the-send-email-action"></a>De actie ' e-mail verzenden ' toevoegen

Voeg nu een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe waarmee een e-mail bericht wordt verzonden wanneer een nieuw item in de RSS-feed wordt weer gegeven.

1. Selecteer **nieuwe stap**onder de trigger **Wanneer een feed-item wordt gepubliceerd** .

   ![Onder trigger selecteert u ' nieuwe stap '](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Onder **Kies een actie** en het zoekvak, selecteert u **alle**.

1. Typ `send an email` in het zoekvak om connectors te vinden die deze actie aanbieden. Selecteer in de lijst acties de actie ' een e-mail verzenden ' voor de e-mail service die u wilt gebruiken. In dit voor beeld wordt de Office 365 Outlook-Connector gebruikt, die de actie **een E-mail verzenden** heeft.

   ![Selecteer de actie ' een e-mail verzenden ' voor Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Als u de lijst met acties wilt filteren op een bepaalde app of service, kunt u eerst die app of service selecteren:

   * Voor werk- of schoolaccounts van Azure selecteert u Outlook van Office 365.
   * Selecteer Outlook.com voor persoonlijke Microsoft-accounts.

1. Als uw geselecteerde e-mail Connector u vraagt om uw identiteit te verifiëren, voltooit u die stap nu om een verbinding tot stand te brengen tussen uw logische app en uw e-mail service.

   > [!NOTE]
   > In dit specifieke voor beeld moet u uw identiteit hand matig verifiëren. Connectors waarvoor verificatie is vereist, verschillen echter in de verificatie typen die ze ondersteunen. U hebt ook opties voor het instellen van de manier waarop u de verificatie wilt afhandelen. Wanneer u bijvoorbeeld Azure Resource Manager sjablonen gebruikt voor implementatie, kunt u para meters en de beveiliging verbeteren voor invoer die u vaak of eenvoudig wilt wijzigen, zoals verbindings gegevens. Zie deze onderwerpen voor meer informatie:
   >
   > * [Sjabloon parameters voor implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [OAuth-verbindingen autoriseren](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Toegang verifiëren met beheerde identiteiten](../logic-apps/create-managed-service-identity.md)
   > * [Verbindingen voor implementatie van logische apps verifiëren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. In de actie **Een e-mail verzenden** geeft u de gegevens op die u in het e-mailbericht wilt opnemen.

   1. Voer het e-mailadres van de ontvanger in het vak **Aan** in. Voor test doeleinden kunt u uw e-mail adres gebruiken.

      Negeer voorlopig de lijst **Dynamische inhoud toevoegen** die wordt weergegeven. Wanneer u in bepaalde invoervakken klikt, wordt deze lijst geopend met alle beschikbare parameters uit de vorige stap die u als invoer in uw werkstroom kunt opnemen.

   1. Voer in het vak **Onderwerp** deze tekst met een afsluitende spatie in: `New RSS item: `

      ![Voer in de eigenschap onderwerp uw e-mail onderwerp in](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. In de lijst **Dynamische inhoud toevoegen** selecteert u **Feedtitel** om de titel van het RSS-item op te nemen.

      ![Selecteer in de lijst met dynamische inhoud de eigenschap "invoer titel"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Wanneer u klaar bent, ziet het onderwerp van de e-mail eruit zoals in dit voorbeeld:

      ![Voor beeld van een e-mail onderwerp voor de titel van de toegevoegde feed](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Als een 'For each'-lus in de ontwerpfunctie wordt weergegeven, hebt u een token voor een matrix geselecteerd, bijvoorbeeld het token **categories-Item**. Voor dit type tokens voegt de ontwerpfunctie automatisch deze lus toe rond de actie die verwijst naar dat token. Op die manier wordt dezelfde actie uitgevoerd voor elk matrixitem. Als u de lus wilt verwijderen, selecteert u de **weglatings** tekens (**...**) op de titel balk van de lus en selecteert u vervolgens **verwijderen**.

   1. In het vak voor de **hoofdtekst** voert u deze tekst in en selecteert u deze tokens voor de inhoud van het e-mailbericht. Als u lege regels wilt toevoegen in een invoervak, drukt u op Shift + Enter.

      ![Eigenschappen voor inhoud van e-mail bericht selecteren](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Eigenschap | Beschrijving |
      |----------|-------------|
      | **Feedtitel** | De titel van het item |
      | **Feed gepubliceerd op** | De publicatiedatum en -tijd van het item |
      | **Primaire feedkoppeling** | De URL voor het item |
      |||

1. Sla uw logische app op.

Test vervolgens de logische app.

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

Als u de logische app hand matig wilt starten, selecteert u **uitvoeren**op de werk balk van de ontwerp functie. Of wacht tot de logische app de RSS-feed op basis van uw opgegeven schema (elke minuut) heeft gecontroleerd. Als de RSS-feed nieuwe items heeft, verzendt uw logische app een e-mailbericht voor elk nieuw item. Anders wacht de logische app tot het volgende interval voordat opnieuw een controle wordt uitgevoerd. Als u geen een e-mailberichten ontvangt, controleert u de map Ongewenste e-mail.

Dit is een voorbeeld van een e-mailbericht dat deze logische app verzendt.

![Voorbeeld bericht verzonden wanneer het nieuwe RSS-feed-item wordt weer gegeven](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technisch gezien, wanneer de trigger de RSS-feed controleert en nieuwe items vindt, wordt de trigger geactiveerd en maakt de Azure Logic Apps-Engine een exemplaar van de werk stroom van uw logische app waarmee de acties in de werk stroom worden uitgevoerd. Als de trigger geen nieuwe items vindt, wordt deze niet geactiveerd en wordt het instantiëren van de werkstroom overgeslagen.

Gefeliciteerd, u hebt nu uw eerste logische app gemaakt en uitgevoerd met de Azure Portal.

## <a name="clean-up-resources"></a>Resources opschonen

Als u dit voorbeeld niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat.

1. Selecteer in het hoofd menu van Azure de optie **resource groepen**en selecteer vervolgens de resource groep van de logische app. Selecteer in het deel venster **overzicht** de optie **resource groep verwijderen**.

   ![Resource groep zoeken, selecteren en verwijderen](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Wanneer het bevestigings venster wordt weer gegeven, voert u de naam van de resource groep in en selecteert u **verwijderen**.

   ![Selecteer verwijderen om het verwijderen te bevestigen](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Wanneer u een logische app verwijdert, worden geen nieuwe uitvoeringen gemaakt. Alle uitvoeringen die bezig zijn en wachten op uitvoering worden geannuleerd. Als u duizenden uitvoeringen hebt, kan de annulering een aanzienlijke tijd in beslag nemen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u uw eerste logische app gemaakt die controleert op de aanwezigheid van RSS-updates op basis van de opgegeven planning (elke minuut) en onderneemt actie (stuurt e-mail) wanneer er updates zijn. Als u meer wilt weten, gaat u verder met deze zelfstudie om geavanceerdere werkstromen op basis van een planning te maken:

> [!div class="nextstepaction"]
> [Verkeer controleren met een logische app op basis van een planning](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
