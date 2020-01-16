---
title: Wijzigingen in virtuele machines bewaken-Azure Event Grid & Logic Apps
description: Controleren op wijzigingen in virtuele machines (Vm's) met behulp van Azure Event Grid en Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: f5aac7fe63b2afc997ff69e5d976c755440c1bea
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982571"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Zelf studie: wijzigingen van virtuele machines bewaken met behulp van Azure Event Grid en Logic Apps

Als u specifieke gebeurtenissen wilt bewaken en erop wilt reageren die zich voordoen in azure-resources of bronnen van derden, kunt u taken automatiseren en uitvoeren als werk stroom door een [logische app](../logic-apps/logic-apps-overview.md) te maken die gebruikmaakt van minimale code. Deze resources kunnen gebeurtenissen publiceren in een [Azure Event grid](../event-grid/overview.md). Het gebeurtenisraster verstuurt die gebeurtenissen vervolgens naar abonnees die webhooks, wachtrijen of [gebeurtenishubs](../event-hubs/event-hubs-what-is-event-hubs.md) als eindpunt hebben. Als abonnee kan uw logische app wachten op die gebeurtenissen vanuit het gebeurtenis raster voordat automatische werk stromen worden uitgevoerd om taken uit te voeren.

Dit zijn enkele voorbeelden van gebeurtenissen die uitgevers naar abonnees kunnen versturen via de service Azure Event Grid:

* Maken, lezen, bijwerken of verwijderen van een resource. U kunt bijvoorbeeld controleren op wijzigingen die extra kosten op uw Azure-abonnement tot gevolg kunnen hebben en dus van invloed zijn op de hoogte van uw factuur.

* Toevoegen of verwijderen van een persoon uit een Azure-abonnement.

* Uitvoeren van een bepaalde actie door uw app.

* Nieuw bericht in een wachtrij.

In deze zelf studie maakt u een logische app die wijzigingen aan een virtuele machine bewaakt en e-mail over deze wijzigingen verzendt. Wanneer u een logische app maakt met een gebeurtenisabonnement voor een Azure-resource, worden gebeurtenissen vanuit die resource via een gebeurtenisraster naar de logische app geleid. In de zelfstudie gaat u deze logische app bouwen:

![Overzicht - wijzigingen in virtuele machines bewaken met een gebeurtenisraster en een logische app](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een logische app maken die gebeurtenissen uit een gebeurtenisraster bewaakt.
> * Een voorwaarde toevoegen die specifiek controleert op wijzigingen van de virtuele machine.
> * Een e-mail verzenden wanneer de virtuele machine wordt gewijzigd.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een e-mail account van een e-mail provider die wordt ondersteund door Logic Apps voor het verzenden van meldingen, zoals Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](/connectors/).

  In deze zelf studie wordt een Office 365 Outlook-account gebruikt. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

* Een [virtuele machine](https://azure.microsoft.com/services/virtual-machines) die alleen voor komt in een eigen Azure-resource groep. Als u dit nog niet hebt gedaan, maakt u een virtuele machine met behulp van de [zelf studie een VM maken](../virtual-machines/windows/quick-create-portal.md). U [hoeft verder niets te doen](../event-grid/overview.md) om gebeurtenissen te laten publiceren door de virtuele machine.

## <a name="create-blank-logic-app"></a>Een lege, logische app maken

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Selecteer in het hoofd menu van Azure **een resource maken** > **integratie** > **logische app**.

   ![Logische app maken](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Onder **Logic app**geeft u informatie op over de logische app-resource. Als u gereed bent, selecteert u **Maken**.

   ![Gegevens van logische app opgeven](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Eigenschap | Verplicht | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Naam** | Ja | <*logic-app-name*> | Geef een unieke naam op voor uw logische app. |
   | **Abonnement** | Ja | <*Azure-subscription-name*> | Selecteer hetzelfde Azure-abonnement voor alle services in deze zelf studie. |
   | **Resourcegroep** | Ja | <*Azure-resource-group*> | De naam van de Azure-resource groep voor uw logische app, die u kunt selecteren voor alle services in deze zelf studie. |
   | **Locatie** | Ja | <*Azure-regio*> | Selecteer dezelfde regio voor alle services in deze tutorial. |
   |||

1. Nadat Azure uw logische app heeft geïmplementeerd, toont de Logic Apps Designer een pagina met een introductie video en veelgebruikte triggers. Scrol voorbij de video en triggers.

1. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Sjabloon voor logische apps selecteren](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   De Logic Apps designer toont nu de [*Triggers*](../logic-apps/logic-apps-overview.md#logic-app-concepts) die u kunt gebruiken om uw logische app te starten. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Telkens wanneer de trigger wordt geactiveerd, wordt door Azure Logic Apps een workflowexemplaar gemaakt waarmee uw logische app wordt uitgevoerd.

## <a name="add-an-event-grid-trigger"></a>Een Event Grid trigger toevoegen

Voeg nu de Event Grid trigger toe, die u gebruikt om de resource groep voor de virtuele machine te bewaken.

1. Voer op de ontwerp functie in het zoekvak `event grid` in als uw filter. Selecteer in de lijst triggers de trigger **Wanneer een bron gebeurtenis zich voordoet** .

   ![Selecteer deze trigger: ' bij een resource gebeurtenis '](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Meld u desgevraagd aan bij Azure Event Grid met de referenties van uw Azure-account. Controleer in de lijst met **tenants** de Azure Active Directory-Tenant die is gekoppeld aan uw Azure-abonnement, of de juiste Tenant wordt weer gegeven, bijvoorbeeld:

   ![Aanmelden met uw Azure-referenties](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Als u zich aanmeldt met een persoonlijk Microsoft-account, zoals @outlook.com of @hotmail.com, wordt de trigger voor het gebeurtenisraster mogelijk niet juist weergegeven. Als tijdelijke oplossing selecteert u [verbinding maken met Service-Principal](../active-directory/develop/howto-create-service-principal-portal.md)of wordt u geverifieerd als lid van de Azure Active Directory dat is gekoppeld aan uw Azure-abonnement, bijvoorbeeld de *gebruikers naam*@emailoutlook.onmicrosoft.com.

1. Abonneer u nu op uw logische app op gebeurtenissen van de uitgever. Geef de details op over uw gebeurtenis abonnement, zoals wordt beschreven in de volgende tabel, bijvoorbeeld:

   ![Details opgeven voor gebeurtenisabonnement](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Eigenschap | Verplicht | Waarde | Beschrijving |
   | -------- | -------- | ----- | ----------- |
   | **Abonnement** | Ja | <*event-publisher-Azure-subscription-name*> | Selecteer de naam voor het Azure-abonnement dat is gekoppeld aan de uitgever van de *gebeurtenis*. Voor deze zelf studie selecteert u de naam van het Azure-abonnement voor uw virtuele machine. |
   | **Resourcetype** | Ja | <*event-publisher-Azure-resource-type*> | Selecteer het Azure-resource type voor de gebeurtenis Uitgever. Zie [Azure resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md)voor meer informatie over Azure-resource typen. Voor deze zelf studie selecteert u de `Microsoft.Resources.ResourceGroups` waarde voor het bewaken van Azure-resource groepen. |
   | **Resourcenaam** |  Ja | <*event-publisher-Azure-resource-name*> | Selecteer de naam van de Azure-resource voor de uitgever van de gebeurtenis. Deze lijst is afhankelijk van het bron type dat u hebt geselecteerd. Voor deze zelf studie selecteert u de naam voor de Azure-resource groep die uw virtuele machine bevat. |
   | **Gebeurtenis type-item** |  Nee | <*event-types*> | Selecteer een of meer specifieke gebeurtenis typen om te filteren en te verzenden naar uw event grid. U kunt deze gebeurtenis typen bijvoorbeeld eventueel toevoegen om te detecteren wanneer resources worden gewijzigd of verwijderd: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Zie deze onderwerpen voor meer informatie: <p><p>- [Azure Event grid-gebeurtenis schema voor resource groepen](../event-grid/event-schema-resource-groups.md) <br>- [begrijpen gebeurtenis filters](../event-grid/event-filtering.md) <br>[filter gebeurtenissen - voor Event grid](../event-grid/how-to-filter-events.md) |
   | Selecteer **nieuwe para meter toevoegen**en selecteer vervolgens de gewenste eigenschappen om optionele eigenschappen toe te voegen. | Nee | {Zie beschrijvingen} | * **voorvoegsel filter**: laat deze eigenschap leeg voor deze zelf studie. Het standaardgedrag komt overeen met alle waarden. U kunt echter een voorvoegseltekenreeks opgeven als een filter, bijvoorbeeld een pad en een parameter voor een specifieke resource. <p>* **achtervoegsel filter**: laat deze eigenschap leeg voor deze zelf studie. Het standaardgedrag komt overeen met alle waarden. U kunt echter een achtervoegseltekenreeks opgeven als een filter, bijvoorbeeld een bestandsnaamextensie, als u alleen bepaalde bestandstypen wilt gebruiken. <p>* **abonnements naam**: voor deze zelf studie kunt u een unieke naam opgeven voor uw gebeurtenis abonnement. |
   |||

1. Sla uw logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie. Als u de details van een actie in uw logische app wilt samen vouwen en verbergen, selecteert u de titel balk van de actie.

   ![Uw logische app opslaan](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Wanneer u uw logische app met een trigger voor het gebeurtenisraster opslaat, maakt Azure automatisch een gebeurtenisabonnement voor uw logische app voor de geselecteerde resource. Wanneer de resource nu een gebeurtenis naar het gebeurtenisraster publiceert, wordt die gebeurtenis automatisch door het raster naar de logische app verstuurd. Deze gebeurtenis triggert de logische app, en maakt vervolgens een exemplaar van de werkstroom die u in de volgende stappen definieert. Ten slotte wordt de werkstroom uitgevoerd.

Uw logische app is nu live en luistert naar gebeurtenissen uit het gebeurtenisraster, maar kan verder nog niks totdat u acties aan de werkstroom toevoegt.

## <a name="add-a-condition"></a>Een voorwaarde toevoegen

Als u wilt dat uw logische app alleen wordt uitgevoerd wanneer er een bepaalde gebeurtenis of bewerking plaatsvindt, voegt u een voor waarde toe waarmee wordt gecontroleerd op de `Microsoft.Compute/virtualMachines/write` bewerking. Als aan deze voorwaarde wordt voldaan (is Waar), verstuurt de logische app een e-mail met meer gegevens van de bijgewerkte virtuele machine.

1. Selecteer in de ontwerp functie voor logische apps onder de trigger Event grid de optie **nieuwe stap**.

   ![Selecteer ' nieuwe stap '](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Voer in het zoekvak onder **Kies een actie**`condition` in als uw filter. Selecteer in de lijst acties de actie **voor de voor waarde** .

   ![Een voorwaarde toevoegen](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Er wordt een lege voorwaarde voor uw werkstroom toegevoegd, inclusief de te volgen actiepaden wanneer de voorwaarde Waar of Onwaar is.

   ![Er wordt een lege voor waarde weer gegeven](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Wijzig de naam van de voorwaarde titel in `If a virtual machine in your resource group has changed`. Selecteer op de titel balk van de voor waarde de knop met weglatings tekens ( **...** ) en selecteer **naam wijzigen**.

   ![Naam van de voor waarde wijzigen](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Maak een voor waarde waarmee de gebeurtenis `body` wordt gecontroleerd op een `data` object waarvan de eigenschap `operationName` gelijk is aan de `Microsoft.Compute/virtualMachines/write` bewerking. Lees hier meer over het [gebeurtenisschema van Event Grid](../event-grid/event-schema.md).

   1. Klink in het linkervakje in de eerste rij onder **En**. Selecteer in de lijst met dynamische inhoud die wordt weer gegeven **expressie**.

      ![Selecteer expressie om de expressie-editor te openen](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. Voer deze expressie in in de expressie-editor, waarmee de naam van de bewerking uit de trigger wordt geretourneerd en selecteer **OK**:

      `triggerBody()?['data']['operationName']`

      Bijvoorbeeld:

      ![Voer de expressie in om de bewerkings naam te extra heren](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. In het middelste vakje behoud u **is gelijk aan** voor de operator.

   1. Voer in het vak rechts deze waarde in, die de specifieke bewerking is die u wilt bewaken:

      `Microsoft.Compute/virtualMachines/write`

   De voor waarde voor de voltooide situatie ziet er nu uit als in dit voor beeld:

   ![De voor waarde is voltooid waarmee de bewerking wordt vergeleken](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Als u overschakelt van de ontwerp weergave naar de code weergave en terug naar de ontwerp weergave, wordt de expressie die u in de voor waarde hebt opgegeven omgezet in de **gegevens. bewerkings** token:

   ![Opgeloste tokens in voor waarde](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Sla uw logische app op.

## <a name="send-email-notifications"></a>E-mailmeldingen verzenden

Voeg nu een [*actie*](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe, zodat u een e-mail bericht kunt ontvangen wanneer de opgegeven voor waarde waar is.

1. Selecteer **een actie toevoegen**in het vak **Indien waar** van de voor waarde.

   ![Actie toevoegen voor wanneer de voorwaarde waar is](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Voer in het zoekvak onder **Kies een actie**`send an email` in als uw filter. Zoek en selecteer de bijbehorende connector op basis van uw e-mailprovider. Selecteer vervolgens de actie 'e-mail verzenden' voor de connector. Bijvoorbeeld:

   * Selecteer voor een werk- of schoolaccount van Azure de Office 365 Outlook-connector.

   * Selecteer voor een persoonlijk Microsoft-account de Outlook.com-connector.

   * Selecteer voor een Gmail-account de Gmail-connector.

   Deze zelf studie gaat verder met de Office 365 Outlook-Connector. Als u een andere provider gebruikt, blijven de stappen gelijk, maar uw gebruikers interface kan er iets anders uitzien.

   ![De actie 'Een e-mail verzenden' selecteren](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Als u nog geen verbinding voor uw e-mailprovider hebt, meldt u zich aan bij uw e-mailaccount wanneer er om verificatie wordt gevraagd.

1. Wijzig de naam van de actie e-mail verzenden naar deze titel: `Send email when virtual machine updated`

1. Geef informatie op over het e-mail adres zoals opgegeven in de volgende tabel:

   ![Informatie opgeven over e-mail actie](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Als u de uitvoer van de vorige stappen in uw werk stroom wilt selecteren, klikt u in een invoervak zodat de lijst met dynamische inhoud wordt weer gegeven of selecteert u **dynamische inhoud toevoegen**. Voor meer resultaten selecteert u **meer weer geven** voor elke sectie in de lijst. Als u de lijst met dynamische inhoud wilt sluiten, selecteert u opnieuw **dynamische inhoud toevoegen** .

   | Eigenschap | Verplicht | Waarde | Beschrijving |
   | -------- | -------- | ----- | ----------- |
   | **Aan** | Ja | <*ontvanger\@domein*> | Voer het e-mailadres van de ontvanger in. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   | **Onderwerp** | Ja | `Resource updated:` **onderwerp** | Voer de inhoud van het onderwerp van de e-mail in. Voer voor deze zelf studie de opgegeven tekst in en selecteer het **onderwerps** veld van de gebeurtenis. Hier bevat het onderwerp van de e-mail de naam voor de bijgewerkte resource (virtuele machine). |
   | **Hoofdtekst** | Ja | `Resource:` **onderwerp** <p>`Event type:` **gebeurtenis type**<p>`Event ID:` **-id**<p>Tijd van `Time:` **gebeurtenis** | Voer de inhoud voor de hoofdtekst van de e-mail in. Voor deze zelf studie voert u de opgegeven tekst in en selecteert u het **onderwerp**van de gebeurtenis, het **gebeurtenis type**, de **id**en de **gebeurtenis tijd** , zodat uw e-mail de bron bevat die de gebeurtenis, het gebeurtenis type, de tijds tempel van de gebeurtenis en gebeurtenis-id voor de update heeft gestart. Voor deze zelf studie is de resource de Azure-resource groep geselecteerd in de trigger. <p>U kunt lege regels toevoegen aan de inhoud door op Shift+Enter te drukken. |
   ||||

   > [!NOTE]
   > Als u een veld selecteert dat een matrix vertegenwoordigt, wordt in de ontwerpfunctie automatisch een lus **Voor elke** toegevoegd rond de actie die naar de matrix verwijst. Op die manier wordt de actie uitgevoerd voor elk matrixitem.

   De e-mailactie ziet er nu ongeveer uit zoals in dit voorbeeld:

   ![Velden selecteren voor opname in e-mailbericht](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   De voltooide logische app ziet er dan ongeveer zo uit:

   ![Voltooide logische app](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Sla uw logische app op. Selecteer de titel balk van de actie om de details van elke actie in uw logische app samen te vouwen en te verbergen.

   Uw logische app is nu live, maar er wordt gewacht op wijzigingen van de virtuele machine voordat er iets gebeurt. Ga door naar de volgende sectie als u de logische app nu wilt testen.

## <a name="test-your-logic-app-workflow"></a>De werkstroom van uw logische app testen

1. Om te controleren of uw logische app de opgegeven gebeurtenissen ontvangt, moet u de virtuele machine bijwerken.

   U kunt bijvoorbeeld de grootte van de virtuele machine aanpassen in Azure Portal of [met Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Binnen enkele ogenblikken moet u dan een e-mailbericht krijgen. Bijvoorbeeld:

   ![E-mail over update van virtuele machine](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Als u de uitvoerings-en trigger geschiedenis voor uw logische app wilt bekijken, selecteert u **overzicht**in het menu van de logische app. Als u meer details over een uitvoering wilt weer geven, selecteert u de rij voor die uitvoering.

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

* Als u uw logische app niet meer wilt uitvoeren, maar uw werk niet wilt verwijderen, schakelt u uw app uit. Selecteer **overzicht**in het menu van de logische app. Selecteer **uitschakelen**op de werk balk.

  ![De logische app uitschakelen](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Als u het menu van de logische app niet ziet, probeer dan terug te gaan naar het Azure-dashboard. Open uw logische app dan opnieuw.

* Als u uw logische app permanent wilt verwijderen, selecteert u **overzicht**in het menu van de logische app. Selecteer **verwijderen**op de werk balk. Bevestig dat u uw logische app wilt verwijderen en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* [Aangepaste gebeurtenissen maken en routeren met behulp van Event Grid](../event-grid/custom-event-quickstart.md)
