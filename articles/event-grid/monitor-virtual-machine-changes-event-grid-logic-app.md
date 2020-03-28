---
title: Wijzigingen in virtuele machines bewaken - Azure Event Grid & Logic Apps
description: Controleren op wijzigingen in virtuele machines (VM's) met Azure Event Grid en Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: f5aac7fe63b2afc997ff69e5d976c755440c1bea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75982571"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Zelfstudie: Wijzigingen in virtuele machines controleren met Azure Event Grid en Logic Apps

Als u specifieke gebeurtenissen wilt controleren en erop wilt reageren die plaatsvinden in Azure-bronnen of bronnen van derden, u taken als werkstroom automatiseren en uitvoeren door een [logische app](../logic-apps/logic-apps-overview.md) te maken die minimale code gebruikt. Deze bronnen kunnen gebeurtenissen publiceren naar een [Azure-gebeurtenisraster](../event-grid/overview.md). Het gebeurtenisraster verstuurt die gebeurtenissen vervolgens naar abonnees die webhooks, wachtrijen of [gebeurtenishubs](../event-hubs/event-hubs-what-is-event-hubs.md) als eindpunt hebben. Als abonnee kan uw logische app wachten tot deze gebeurtenissen van het gebeurtenisraster worden uitgevoerd voordat geautomatiseerde werkstromen worden uitgevoerd om taken uit te voeren.

Dit zijn enkele voorbeelden van gebeurtenissen die uitgevers naar abonnees kunnen versturen via de service Azure Event Grid:

* Maken, lezen, bijwerken of verwijderen van een resource. U kunt bijvoorbeeld controleren op wijzigingen die extra kosten op uw Azure-abonnement tot gevolg kunnen hebben en dus van invloed zijn op de hoogte van uw factuur.

* Toevoegen of verwijderen van een persoon uit een Azure-abonnement.

* Uitvoeren van een bepaalde actie door uw app.

* Nieuw bericht in een wachtrij.

Deze zelfstudie maakt een logische app die wijzigingen in een virtuele machine bewaakt en e-mails over deze wijzigingen verzendt. Wanneer u een logische app maakt met een gebeurtenisabonnement voor een Azure-resource, worden gebeurtenissen vanuit die resource via een gebeurtenisraster naar de logische app geleid. In de zelfstudie gaat u deze logische app bouwen:

![Overzicht - wijzigingen in virtuele machines bewaken met een gebeurtenisraster en een logische app](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een logische app maken die gebeurtenissen uit een gebeurtenisraster bewaakt.
> * Een voorwaarde toevoegen die specifiek controleert op wijzigingen van de virtuele machine.
> * Een e-mail verzenden wanneer de virtuele machine wordt gewijzigd.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een e-mailaccount van een e-mailprovider die wordt ondersteund door Logic Apps voor het verzenden van meldingen, zoals Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](/connectors/).

  In deze zelfstudie wordt een Office 365 Outlook-account gebruikt. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

* Een [virtuele machine](https://azure.microsoft.com/services/virtual-machines) die alleen is in zijn eigen Azure-brongroep. Als u dit nog niet hebt gedaan, maakt u een virtuele machine via de [zelfstudie Van een VM maken.](../virtual-machines/windows/quick-create-portal.md) U [hoeft verder niets te doen](../event-grid/overview.md) om gebeurtenissen te laten publiceren door de virtuele machine.

## <a name="create-blank-logic-app"></a>Een lege, logische app maken

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Selecteer in het hoofdmenu van Azure de optie Een > **logica-app**voor > **bronintegratie** **maken**.

   ![Logische app maken](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Geef onder **Logic App**informatie over uw logische app-bron. Als u gereed bent, selecteert u **Maken**.

   ![Gegevens van logische app opgeven](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Naam** | Ja | <*logische-app-naam*> | Geef een unieke naam op voor uw logische app. |
   | **Abonnement** | Ja | <*Azure-abonnementsnaam*> | Selecteer hetzelfde Azure-abonnement voor alle services in deze zelfstudie. |
   | **Resourcegroep** | Ja | <*Azure-resourcegroep*> | De naam van de Azure-brongroep voor uw logische app, die u selecteren voor alle services in deze zelfstudie. |
   | **Locatie** | Ja | <*Azure-regio*> | Selecteer dezelfde regio voor alle services in deze tutorial. |
   |||

1. Nadat Azure uw logische app heeft geïmplementeerd, toont de Logic Apps Designer een pagina met een introductievideo en veelgebruikte triggers. Scrol voorbij de video en triggers.

1. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Sjabloon logische app selecteren](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   De Logic Apps Designer toont u nu de [*triggers*](../logic-apps/logic-apps-overview.md#logic-app-concepts) die u gebruiken om uw logische app te starten. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Elke keer dat de trigger wordt geactiveerd, maakt Azure Logic Apps een werkstroominstantie waarmee uw logische app wordt uitgevoerd.

## <a name="add-an-event-grid-trigger"></a>Een trigger voor gebeurtenisraster toevoegen

Voeg nu de trigger van eventgrid toe, die u gebruikt om de brongroep voor uw virtuele machine te controleren.

1. Voer in het zoekvak in `event grid` als filter in het zoekvak. Selecteer in de lijst triggers de **trigger Wanneer een resourcegebeurtenis plaatsvindt.**

   ![Selecteer deze trigger: "Op een resourcegebeurtenis"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Meld u desgevraagd aan bij Azure Event Grid met uw Azure-accountreferenties. Controleer in de lijst **Tenant,** waarin de Azure Active Directory-tenant wordt weergegeven die is gekoppeld aan uw Azure-abonnement, of de juiste tenant wordt weergegeven, bijvoorbeeld:

   ![Aanmelden met uw Azure-referenties](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Als u zich aanmeldt met een persoonlijk Microsoft-account, zoals @outlook.com of @hotmail.com, wordt de trigger voor het gebeurtenisraster mogelijk niet juist weergegeven. Selecteer Verbinding maken [met Serviceprincipal](../active-directory/develop/howto-create-service-principal-portal.md)of verifieer t.a.v. als lid van de Azure Active Directory die is gekoppeld aan uw *Azure-abonnement,*@emailoutlook.onmicrosoft.combijvoorbeeld gebruikersnaam .

1. Abonneer je nu op je logica-app op gebeurtenissen van de uitgever. Geef de details op over uw evenementabonnement zoals beschreven in de volgende tabel, bijvoorbeeld:

   ![Details opgeven voor gebeurtenisabonnement](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   | -------- | -------- | ----- | ----------- |
   | **Abonnement** | Ja | <*naam gebeurtenis-uitgever-Azure-abonnement*> | Selecteer de naam voor het Azure-abonnement dat is gekoppeld aan de *gebeurtenisuitgever*. Selecteer voor deze zelfstudie de naam van het Azure-abonnement voor uw virtuele machine. |
   | **Resourcetype** | Ja | <*event-publisher-Azure-resource-type*> | Selecteer het Azure-brontype voor de gebeurtenis-uitgever. Zie [Azure-bronproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md)voor meer informatie over Azure-brontypen. Selecteer voor deze `Microsoft.Resources.ResourceGroups` zelfstudie de waarde voor het bewaken van Azure-brongroepen. |
   | **Resourcenaam** |  Ja | <*naam gebeurtenis-uitgever-Azure-resource*> | Selecteer de azure-bronnaam voor de gebeurtenis-uitgever. Deze lijst is afhankelijk van het resourcetype dat u hebt geselecteerd. Selecteer voor deze zelfstudie de naam voor de Azure-brongroep die uw virtuele machine bevat. |
   | **Gebeurtenistypeitem** |  Nee | <*gebeurtenistypen*> | Selecteer een of meer specifieke gebeurtenistypen die u wilt filteren en naar uw gebeurtenisraster wilt verzenden. U bijvoorbeeld optioneel deze gebeurtenistypen toevoegen om te detecteren wanneer resources worden gewijzigd of verwijderd: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Zie deze onderwerpen voor meer informatie: <p><p>- [Azure Event Grid-gebeurtenisschema voor resourcegroepen](../event-grid/event-schema-resource-groups.md) <br>- [Gebeurtenisfiltering begrijpen](../event-grid/event-filtering.md) <br>- [Gebeurtenissen filteren op gebeurtenisraster](../event-grid/how-to-filter-events.md) |
   | Als u optionele eigenschappen wilt toevoegen, selecteert u **Nieuwe parameter toevoegen**en selecteert u vervolgens de gewenste eigenschappen. | Nee | {zie de beschrijvingen} | * **Voorvoegselfilter:** Laat deze eigenschap voor deze zelfstudie leeg. Het standaardgedrag komt overeen met alle waarden. U kunt echter een voorvoegseltekenreeks opgeven als een filter, bijvoorbeeld een pad en een parameter voor een specifieke resource. <p>* **Achtervoegselfilter:** Laat deze eigenschap voor deze zelfstudie leeg. Het standaardgedrag komt overeen met alle waarden. U kunt echter een achtervoegseltekenreeks opgeven als een filter, bijvoorbeeld een bestandsnaamextensie, als u alleen bepaalde bestandstypen wilt gebruiken. <p>* **Abonnementsnaam:** Voor deze zelfstudie u een unieke naam opgeven voor uw evenementabonnement. |
   |||

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**. Als u de gegevens van een actie wilt samenvouwen en verbergen in uw logische app, selecteert u de titelbalk van de actie.

   ![Uw logische app opslaan](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Wanneer u uw logische app met een trigger voor het gebeurtenisraster opslaat, maakt Azure automatisch een gebeurtenisabonnement voor uw logische app voor de geselecteerde resource. Wanneer de resource nu een gebeurtenis naar het gebeurtenisraster publiceert, wordt die gebeurtenis automatisch door het raster naar de logische app verstuurd. Deze gebeurtenis triggert de logische app, en maakt vervolgens een exemplaar van de werkstroom die u in de volgende stappen definieert. Ten slotte wordt de werkstroom uitgevoerd.

Uw logische app is nu live en luistert naar gebeurtenissen uit het gebeurtenisraster, maar kan verder nog niks totdat u acties aan de werkstroom toevoegt.

## <a name="add-a-condition"></a>Een voorwaarde toevoegen

Als u wilt dat uw logische app alleen wordt uitgevoerd wanneer een specifieke `Microsoft.Compute/virtualMachines/write` gebeurtenis of bewerking plaatsvindt, voegt u een voorwaarde toe die voor de bewerking controleert. Als aan deze voorwaarde wordt voldaan (is Waar), verstuurt de logische app een e-mail met meer gegevens van de bijgewerkte virtuele machine.

1. Selecteer in Logic App Designer onder de trigger van het gebeurtenisraster de optie **Nieuwe stap**.

   ![Selecteer 'Nieuwe stap'](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Voer **onder Een actie kiezen**in `condition` het zoekvak in als filter. Selecteer in de lijst met acties de actie **Voorwaarde.**

   ![Een voorwaarde toevoegen](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Er wordt een lege voorwaarde voor uw werkstroom toegevoegd, inclusief de te volgen actiepaden wanneer de voorwaarde Waar of Onwaar is.

   ![Er verschijnt een lege voorwaarde](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Wijzig de naam `If a virtual machine in your resource group has changed`van de voorwaardetitel in . Selecteer op de titelbalk van de voorwaarde de knop ellips (**... )** en selecteer **Naam wijzigen**.

   ![De naam van de voorwaarde wijzigen](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Maak een voorwaarde die `body` de `data` gebeurtenis `operationName` controleert op een `Microsoft.Compute/virtualMachines/write` object waarbij de eigenschap gelijk is aan de bewerking. Lees hier meer over het [gebeurtenisschema van Event Grid](../event-grid/event-schema.md).

   1. Klink in het linkervakje in de eerste rij onder **En**. Selecteer **Expressie**in de lijst met dynamische inhoud die wordt weergegeven .

      ![Selecteer 'Expressie' om de expressieeditor te openen](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. Voer in de expressieeditor deze expressie in, waarmee de bewerkingsnaam van de trigger wordt geretourneerd en selecteer **OK:**

      `triggerBody()?['data']['operationName']`

      Bijvoorbeeld:

      ![Expressie invoeren om de bedrijfsnaam te extraheren](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. In het middelste vakje behoud u **is gelijk aan** voor de operator.

   1. Voer in het rechtervak deze waarde in, de specifieke bewerking die u moet controleren:

      `Microsoft.Compute/virtualMachines/write`

   Uw voltooide toestand ziet er nu als volgt uit:

   ![Voltooide voorwaarde waarin de bewerking wordt vergeleken](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Als u overschakelt van ontwerpweergave naar codeweergave en terug naar ontwerpweergave, wordt de expressie die u in de voorwaarde hebt opgegeven, opgelost naar het **token data.operationName:**

   ![Opgeloste tokens in conditie](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Sla uw logische app op.

## <a name="send-email-notifications"></a>E-mailmeldingen verzenden

Voeg nu een [*actie*](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe zodat u een e-mail ontvangen wanneer de opgegeven voorwaarde waar is.

1. Selecteer Een **actie toevoegen**in het vak **Als waar** van de voorwaarde .

   ![Actie toevoegen voor wanneer de voorwaarde waar is](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Voer **onder Een actie kiezen**in `send an email` het zoekvak in als filter. Zoek en selecteer de bijbehorende connector op basis van uw e-mailprovider. Selecteer vervolgens de actie 'e-mail verzenden' voor de connector. Bijvoorbeeld:

   * Selecteer voor een werk- of schoolaccount van Azure de Office 365 Outlook-connector.

   * Selecteer voor een persoonlijk Microsoft-account de Outlook.com-connector.

   * Selecteer voor een Gmail-account de Gmail-connector.

   Deze zelfstudie gaat verder met de Office 365 Outlook-connector. Als u een andere provider gebruikt, blijven de stappen hetzelfde, maar ziet uw gebruikersinterface er mogelijk iets anders uit.

   ![De actie 'Een e-mail verzenden' selecteren](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Als u nog geen verbinding voor uw e-mailprovider hebt, meldt u zich aan bij uw e-mailaccount wanneer er om verificatie wordt gevraagd.

1. Wijzig de naam van de actie E-mail verzenden naar deze titel:`Send email when virtual machine updated`

1. Geef informatie over de e-mail zoals opgegeven in de volgende tabel:

   ![Informatie geven over e-mailactie](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Als u uitvoer wilt selecteren uit de vorige stappen in uw werkstroom, klikt u in een bewerkingsvak zodat de lijst met dynamische inhoud wordt weergegeven of selecteert u **Dynamische inhoud toevoegen**. Selecteer **Meer voor** elke sectie in de lijst voor meer resultaten. Als u de lijst met dynamische inhoud wilt sluiten, selecteert u Dynamische inhoud opnieuw **toevoegen.**

   | Eigenschap | Vereist | Waarde | Beschrijving |
   | -------- | -------- | ----- | ----------- |
   | **Aan** | Ja | <*Ontvangersdomein\@*> | Voer het e-mailadres van de ontvanger in. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   | **Onderwerp** | Ja | `Resource updated:` **Onderwerp** | Voer de inhoud van het onderwerp van de e-mail in. Voer voor deze zelfstudie de opgegeven tekst in en selecteer het **veld Onderwerp van** de gebeurtenis. Hier bevat het onderwerp van de e-mail de naam voor de bijgewerkte resource (virtuele machine). |
   | **Hoofdtekst** | Ja | `Resource:` **Onderwerp** <p>`Event type:`**Gebeurtenistype**<p>`Event ID:`**ID**<p>`Time:`**Gebeurtenistijd** | Voer de inhoud voor de hoofdtekst van de e-mail in. Voer voor deze zelfstudie de opgegeven tekst in en selecteer de velden **Onderwerp,** **Gebeurtenistype,** **ID**en **Gebeurtenistijd** van de gebeurtenis, zodat uw e-mail de bron bevat die de gebeurtenis, gebeurtenistype, gebeurtenistijdstempel en gebeurtenis-id voor de update heeft geactiveerd. Voor deze zelfstudie is de bron de Azure-brongroep die is geselecteerd in de trigger. <p>U kunt lege regels toevoegen aan de inhoud door op Shift+Enter te drukken. |
   ||||

   > [!NOTE]
   > Als u een veld selecteert dat een matrix vertegenwoordigt, wordt in de ontwerpfunctie automatisch een lus **Voor elke** toegevoegd rond de actie die naar de matrix verwijst. Op die manier wordt de actie uitgevoerd voor elk matrixitem.

   De e-mailactie ziet er nu ongeveer uit zoals in dit voorbeeld:

   ![Velden selecteren voor opname in e-mailbericht](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   De voltooide logische app ziet er dan ongeveer zo uit:

   ![Voltooide logische app](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Sla uw logische app op. Als u de gegevens van elke actie in uw logische app wilt samenvouwen en verbergen, selecteert u de titelbalk van de actie.

   Uw logische app is nu live, maar er wordt gewacht op wijzigingen van de virtuele machine voordat er iets gebeurt. Ga door naar de volgende sectie als u de logische app nu wilt testen.

## <a name="test-your-logic-app-workflow"></a>De werkstroom van uw logische app testen

1. Om te controleren of uw logische app de opgegeven gebeurtenissen ontvangt, moet u de virtuele machine bijwerken.

   U kunt bijvoorbeeld de grootte van de virtuele machine aanpassen in Azure Portal of [met Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Binnen enkele ogenblikken moet u dan een e-mailbericht krijgen. Bijvoorbeeld:

   ![E-mail over update van virtuele machine](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Als u de uitvoeringen en triggergeschiedenis voor uw logische app wilt bekijken, selecteert u **Overzicht**in het menu van de logische app . Als u meer details over een run wilt weergeven, selecteert u de rij voor die run.

   ![Uitvoeringsgeschiedenis van logische app](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Als u de in- en uitvoer van elke stap wilt bekijken, vouwt u de stap uit die u wilt bekijken. Aan de hand van deze gegevens kunt u problemen met de logische app vaststellen en oplossen.

   ![Details van uitvoeringsgeschiedenis van logische app](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

U hebt nu een logische app gemaakt en uitgevoerd die resourcegebeurtenissen controleert met behulp van een gebeurtenisraster en u een e-mailbericht stuurt wanneer deze gebeurtenissen optreden. Daarnaast hebt u geleerd hoe eenvoudig het is om werkstromen te maken voor het automatiseren van processen en hoe u systemen en cloudservices kunt integreren.

U kunt andere configuratiewijzigingen bewaken met gebeurtenisrasters en logische apps, zoals:

* Een virtuele machine krijgt rechten voor toegangsbeheer op basis van rollen (RBAC).
* Een netwerkbeveiligingsgroep (NSG) op een netwerkinterface (NIC) wordt gewijzigd.
* Schijven voor een virtuele machine worden toegevoegd of verwijderd.
* Er wordt een openbaar IP-adres toegewezen aan de NIC van een virtuele machine.

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie worden resources gebruikt en acties uitgevoerd die kosten voor uw Azure-abonnement met zich mee kunnen brengen. Als u klaar bent met de zelfstudie en het testen, denkt u er dan aan dat u resources waarvoor u geen kosten wilt maken, uitschakelt of verwijdert.

* Als u uw logische app niet meer wilt uitvoeren, maar uw werk niet wilt verwijderen, schakelt u uw app uit. Selecteer **Overzicht**in het menu van de logische app . Selecteer op de werkbalk **Disable**.

  ![De logische app uitschakelen](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Als u het menu van de logische app niet ziet, probeer dan terug te gaan naar het Azure-dashboard. Open uw logische app dan opnieuw.

* Als u uw logische app permanent wilt verwijderen, selecteert u **overzicht**in het menu van de logische app . Selecteer **verwijderen op**de werkbalk . Controleer of u uw logische app wilt verwijderen en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* [Aangepaste gebeurtenissen maken en routeren met behulp van Event Grid](../event-grid/custom-event-quickstart.md)
