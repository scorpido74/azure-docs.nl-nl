---
title: 'Quick Start: uw eerste werk stroom met Azure Logic Apps maken en automatiseren'
description: Maak uw eerste logische app waarmee taken, processen en werk stromen worden geautomatiseerd met behulp van Azure Logic Apps. Maak Logic apps voor systeem integratie en EAI-oplossingen (Enter prise Application Integration) voor uw systemen & Cloud Services.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.openlocfilehash: 2f39678e9fa0ed665863a94767240e2e37eace39
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906140"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>Quickstart: uw eerste geautomatiseerde werkstroom maken met Azure Logic Apps - Azure-portal

In deze snelstart leert u uw eerste geautomatiseerde werkstroom bouwen met [Azure Logic Apps](../logic-apps/logic-apps-overview.md). In dit artikel maakt u een logische app waarmee de RSS-feed van een website regelmatig wordt gecontroleerd op nieuwe items. Als er nieuwe items bestaan, stuurt de logische app een e-mail voor elk item. Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Overzicht: voorbeeld van logische app](./media/quickstart-create-first-logic-app-workflow/overview.png)

Voor het uitvoeren van deze quickstart hebt u een e-mailaccount nodig van een provider die wordt ondersteund door Logic Apps, zoals Outlook van Office 365, Outlook.com en Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](https://docs.microsoft.com/connectors/). Deze logische app maakt gebruik van een Office 365 Outlook-account. Als u een ander e-mailaccount gebruikt, zijn de algemene stappen hetzelfde, maar ziet de gebruikersinterface er misschien iets anders uit.

Als u nog geen abonnement op Azure hebt, [meld u dan nu aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Selecteer in het hoofd menu van Azure **een resource** > **Integration** > **Logic-app**maken.

   ![Logische app maken](./media/quickstart-create-first-logic-app-workflow/create-logic-app.png)

1. Onder **Logische app maken** geeft u informatie op over uw logische app zoals hier wordt weergegeven. Wanneer u klaar bent, selecteert u **maken**.

   ![Gegevens van logische app opgeven](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Name** | <*logic-app-name*> | De naam van de logische app, die alleen letters, cijfers, of de `-` `)`, `(`,, `_` `.` , tekens kan bevatten. In dit voor beeld wordt ' mijn-eerste logica-app ' gebruikt. |
   | **Abonnement** | <*Azure-subscription-name*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | <*Azure-resource-group-name*> | De naam voor de [Azure-resource groep](../azure-resource-manager/resource-group-overview.md) die wordt gebruikt om verwante resources te organiseren. In dit voor beeld wordt ' My-First-LA-RG ' gebruikt. |
   | **Location** | <*Azure-regio*> | De regio waar u de gegevens van uw logische app opslaat. In dit voor beeld wordt ' West US ' gebruikt. |
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u in de Azure-werk balk de optie **meldingen** > **gaat u** naar de resource voor uw geïmplementeerde logische app.

   ![Naar de resource gaan](./media/quickstart-create-first-logic-app-workflow/go-to-logic-app.png)

   U kunt ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps-ontwerpfunctie wordt geopend en u ziet een pagina met een inleidende video en veelgebruikte triggers. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Sjabloon voor lege logische app selecteren](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Vervolgens voegt u een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die wordt geactiveerd zodra er een nieuw RSS-feeditem wordt weergegeven. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-engine een exemplaar van een logische app dat wordt gestart en de werkstroom uitvoert.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>RSS-feed controleren met een trigger

1. Selecteer in de ontwerp functie voor logische apps, onder het zoekvak, de optie **alle**.

1. Typ ' RSS ' in het zoekvak. Selecteer in de lijst triggers deze trigger: **Wanneer een feed-item wordt gepubliceerd-RSS**

   ![Trigger selecteren: 'RSS - Wanneer een feeditem wordt gepubliceerd'](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss.png)

1. Geef deze informatie voor de trigger op zoals hier wordt weer gegeven en beschreven:

   ![Trigger instellen met RSS-feed, frequentie en interval](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss-settings.png)

   | Eigenschap | Waarde | Description |
   |----------|-------|-------------|
   | **De URL voor de RSS-feed** | ```http://feeds.reuters.com/reuters/topNews``` | De koppeling voor de RSS-feed die u wilt bijhouden |
   | **Interval** | 1 | Het aantal intervallen dat tussen controles moet worden gewacht |
   | **Frequentie** | Minuut | De tijdseenheid voor elk interval tussen controles  |
   ||||

   Het interval en de frequentie bepalen samen de planning van de trigger voor uw logische app. Deze logische app controleert de feed elke minuut.

1. Als u de details van de trigger voorlopig wilt verbergen, klikt u in de titelbalk van de trigger.

   ![Shape samenvouwen om details te verbergen](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

Uw logische app is nu live, maar kan alleen de RSS-feed controleren. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="send-email-with-an-action"></a>E-mail versturen bij een actie

Voeg nu een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) waardoor een e-mail wordt verzonden zodra een nieuw item in de RSS-feed wordt weergegeven.

1. Selecteer **nieuwe stap**onder de trigger **Wanneer een feed-item wordt gepubliceerd** .

   ![Een actie toevoegen](./media/quickstart-create-first-logic-app-workflow/add-new-action.png)

1. Onder **Kies een actie** en het zoekvak, selecteert u **alle**.

1. Voer in het zoekvak ' een e-mail verzenden ' in. Selecteer in de lijst met acties de actie 'een e-mail verzenden' voor de gewenste e-mailprovider.

   ![Selecteer deze actie: 'Office 365 Outlook - een e-mail verzenden'](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Als u de lijst met acties wilt filteren op een bepaalde app of service, kunt u eerst die app of service selecteren:

   * Voor werk- of schoolaccounts van Azure selecteert u Outlook van Office 365.
   * Selecteer Outlook.com voor persoonlijke Microsoft-accounts.

1. Als u naar uw referenties wordt gevraagd, meldt u zich aan bij uw e-mailaccount zodat Logic Apps een verbinding met uw e-mailaccount kan maken.

1. In de actie **Een e-mail verzenden** geeft u de gegevens op die u in het e-mailbericht wilt opnemen.

   1. Voer het e-mailadres van de ontvanger in het vak **Aan** in. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken.

      Negeer voorlopig de lijst **Dynamische inhoud toevoegen** die wordt weergegeven. Wanneer u in bepaalde invoervakken klikt, wordt deze lijst geopend met alle beschikbare parameters uit de vorige stap die u als invoer in uw werkstroom kunt opnemen.

   1. Voer in het vak **Onderwerp** deze tekst met een afsluitende spatie in: ```New RSS item:```

      ![Het onderwerp van de e-mail invoeren](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. In de lijst **Dynamische inhoud toevoegen** selecteert u **Feedtitel** om de titel van het RSS-item op te nemen.

      ![Lijst met dynamische inhoud - 'Feedtitel'](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Wanneer u klaar bent, ziet het onderwerp van de e-mail eruit zoals in dit voorbeeld:

      ![Feedtitel toegevoegd](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Als een 'For each'-lus in de ontwerpfunctie wordt weergegeven, hebt u een token voor een matrix geselecteerd, bijvoorbeeld het token **categories-Item**. Voor dit type tokens voegt de ontwerpfunctie automatisch deze lus toe rond de actie die verwijst naar dat token. Op die manier wordt dezelfde actie uitgevoerd voor elk matrixitem. Als u de lus wilt verwijderen, selecteert u de weglatings tekens ( **...** ) op de titel balk van de lus en selecteert u vervolgens **verwijderen**.

   1. In het vak voor de **hoofdtekst** voert u deze tekst in en selecteert u deze tokens voor de inhoud van het e-mailbericht. Als u lege regels wilt toevoegen in een invoervak, drukt u op Shift + Enter.

      ![Inhoud voor de hoofdtekst van de e-mail toevoegen](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Eigenschap | Description |
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

![E-mail wordt verzonden voor nieuw RSS-feeditem](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technisch gezien wordt, als bij controle van de RSS-feed nieuwe items worden aangetroffen, de trigger geactiveerd en maakt de Logic Apps-engine een exemplaar van de werkstroom van uw logische app die de acties in de werkstroom uitvoert. Als de trigger geen nieuwe items vindt, wordt deze niet geactiveerd en wordt het instantiëren van de werkstroom overgeslagen.

Gefeliciteerd, u hebt nu uw eerste logische app gemaakt en uitgevoerd met de Azure Portal.

## <a name="clean-up-resources"></a>Resources opschonen

Als u dit voorbeeld niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat.

1. Selecteer in het hoofd menu van Azure de optie **resource groepen**en selecteer vervolgens de resource groep van de logische app. Selecteer in het deel venster **overzicht** de optie **resource groep verwijderen**.

   !['Resourcegroepen' > 'Overzicht' > 'Resourcegroep verwijderen'](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Voer de naam van de resource groep in als bevestiging en selecteer **verwijderen**.

   ![Verwijdering bevestigen](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Wanneer u een logische app verwijdert, worden geen nieuwe uitvoeringen gemaakt. Alle uitvoeringen die bezig zijn en wachten op uitvoering worden geannuleerd. Als u duizenden uitvoeringen hebt, kan de annulering een aanzienlijke tijd in beslag nemen.

## <a name="get-support"></a>Ondersteuning krijgen

Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u uw eerste logische app gemaakt die controleert op de aanwezigheid van RSS-updates op basis van de opgegeven planning (elke minuut) en onderneemt actie (stuurt e-mail) wanneer er updates zijn. Als u meer wilt weten, gaat u verder met deze zelfstudie om geavanceerdere werkstromen op basis van een planning te maken:

> [!div class="nextstepaction"]
> [Verkeer controleren met een logische app op basis van een planning](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
