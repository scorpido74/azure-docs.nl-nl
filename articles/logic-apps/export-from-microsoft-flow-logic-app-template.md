---
title: Exportstromen van Power Automate naar Azure Logic Apps
description: Flows migreren van Power Automate naar Azure Logic Apps door te exporteren als Azure Resource Manager-sjablonen
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428887"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Stromen exporteren vanuit Power Automate en implementeren in Azure Logic Apps

Als u de mogelijkheden van uw stroom wilt uitbreiden en uitbreiden, u die stroom migreren van [Power Automate](https://flow.microsoft.com) naar Azure [Logic Apps.](../logic-apps/logic-apps-overview.md) U uw stroom exporteren als een Azure Resource Manager-sjabloon voor een logische app, die logische app-sjabloon implementeren in een Azure-brongroep en vervolgens die logische app openen in de Logic App Designer.

> [!NOTE]
> Niet alle Power Automate-connectors zijn beschikbaar in Azure Logic Apps. U stromen importeren met [gelijkwaardige connectors](../connectors/apis-list.md) in Azure Logic Apps. De trigger button, de approval-connector en de meldingsconnector zijn bijvoorbeeld specifiek voor Power Automate.
>
> OpenAPI-gebaseerde stromen die vanuit Power Automate worden geëxporteerd, worden momenteel niet ondersteund voor implementatie als logische app-sjablonen. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De stroom die u vanuit Power Automate wilt exporteren

## <a name="export-a-flow"></a>Een stroom exporteren

1. Meld u aan bij [Power Automate](https://flow.microsoft.com)en selecteer **Mijn stromen**. Zoek en selecteer uw stroom. Selecteer op de werkbalk de knop ellipsen (**...**) Selecteer sjabloon Logische apps **exporteren** > **(.json)**.

   ![Stroom exporteren](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Sla uw sjabloon op de gewenste locatie op.

Zie [Omhoog gaan naar Azure Logic Apps voor](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)meer informatie.

## <a name="deploy-template-by-using-the-azure-portal"></a>Sjabloon implementeren met behulp van de Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-account.

1. Selecteer **Een resource maken** in het hoofdmenu van Azure. Voer in het zoekvak 'sjabloonimplementatie' in. Selecteer **Sjabloonimplementatie (implementeren met aangepaste sjablonen)** en selecteer **Vervolgens Maken**.

   ![Selecteer 'Sjabloonimplementatie'](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Selecteer **onder Aangepaste implementatie**de optie Uw eigen sjabloon maken in de **editor**.

   ![Selecteer 'Uw eigen sjabloon maken in de editor'](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Selecteer **bestand laden**op de werkbalk **Sjabloon bewerken** . Zoek en selecteer de JSON-sjabloon die u hebt geëxporteerd vanuit Power Automate en selecteer **Openen**.

   ![Selecteer 'Bestand laden'](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Nadat de editor de JSON, parameters en resources in uw sjabloon weergeeft, selecteert u **Opslaan**.
  
   ![Sjabloon opslaan](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Geef nu de volgende invoerparameters voor de sjabloon op:

   * Azure-abonnement dat moet worden gebruikt voor facturering
   * Azure-resourcegroep
   * Locatie voor de Azure-brongroep
   * Naam voor de bron van de logische app
   * Locatie voor de logische app-bron, indien deze verschilt van de Azure-brongroep
   * De naam voor eerder gemaakte verbindingen die de logische app opnieuw kan gebruiken

      Als u uw eerste logische app maakt, worden alle verbindingen als nieuw gemaakt, zodat u de standaardnamen accepteren. Anders u de namen opgeven voor eerder gemaakte verbindingen, die u gebruiken voor meerdere logische apps.

   Nadat u deze informatie voor de sjabloon hebt verstrekt, controleert en stemt u ermee in met de algemene voorwaarden van Azure Marketplace voor het maken van de benodigde Azure-resources en het factureren van uw Azure-abonnement dienovereenkomstig en selecteert u **Kopen**.
  
   ![Invoerparameters voor sjabloon opgeven](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure implementeert uw sjabloon als een logische app voor uw opgegeven resourcegroep. Alle logische apps die u migreert vanuit Power Automate worden uitgeschakeld.

1. Voordat u uw logische app activeert, moet u nieuwe verbindingen autoriseren door de volgende stappen te volgen:

   1. Open de logische app die u hebt gemaakt. Selecteer **logic-appdesigner**in het menu van de logische app .

      Elke verbinding waarvoor autorisatie vereist is, toont een waarschuwingspictogram:

      ![Waarschuwingspictogram](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Vouw die stap uit voor elke stap waarvoor een geautoriseerde verbinding nodig is en selecteer **Nieuw toevoegen**.

      ![Nieuwe verbinding toevoegen](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Meld u aan bij elke service of geef de benodigde referenties om de verbinding te autoriseren.

1. Sla uw logische app op. Wanneer u klaar bent om uw logische app te activeren, selecteert u in het menu van de logische app **Overzicht**en selecteert u **Inschakelen**.

   ![Logica-app inschakelen](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Als u dubbele werkstromen wilt voorkomen, moet u ervoor zorgen dat u de oorspronkelijke stroom deactiveert of verwijdert.

## <a name="deploy-template-by-using-visual-studio"></a>Sjabloon implementeren met Visual Studio

Als u Visual Studio hebt ingesteld met de [vereisten](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) voor het maken van logische apps, u uw geëxporteerde sjabloon implementeren vanuit Visual Studio naar Azure Logic Apps.

1. Open in Visual Studio het sjabloonbestand dat u vanuit Power Automate hebt geëxporteerd.

1. Maak in Visual Studio een Azure Resource Group-project en selecteer de sjabloon **Logic App** door de stappen in Quickstart te [volgen: Maak geautomatiseerde taken, processen en werkstromen met Azure Logic Apps - Visual Studio,](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)bijvoorbeeld:

   ![Een Azure-resourcegroepproject maken](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Open vanuit Solution Explorer het bestand **LogicApp.json** als het bestand nog niet is geopend.

1. Kopieer de inhoud van de geëxporteerde sjabloon en overschrijf de inhoud in het bestand **LogicApp.json.**

1. Voordat u uw logische app implementeert, geeft u toestemming voor nieuwe verbindingen door de volgende stappen te volgen:

   1. Open het snelmenu **LogicApp.json** en selecteer **Openen met Logic App Designer**.

      ![Sjabloon openen met Logic App Designer](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Selecteer de optie Azure-abonnement en brongroep die u wilt gebruiken voor het implementeren van uw logische app als u daarom wordt gevraagd.

      ![Azure-abonnements- en brongroep selecteren](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Nadat uw logische app in de ontwerper is weergegeven, worden waarschuwingspictogrammen weergegeven door alle verbindingen waarvoor een autorisatie vereist is:

      ![Verbindingen met waarschuwingspictogrammen](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Vouw die stap uit voor elke stap waarvoor een geautoriseerde verbinding nodig is en selecteer **Nieuw toevoegen**.

      ![Nieuwe verbinding toevoegen](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Meld u aan bij elke service of geef de benodigde referenties om de verbinding te autoriseren.

   1. Sla uw oplossing op voordat u de logische app implementeert.

1. Open in Solution Explorer het snelmenu van het project en selecteer**Nieuw** **implementeren** > . Meld u aan met uw Azure-account als u daarom wordt gevraagd.

1. Bevestig desgevraagd het Azure-abonnement, de Azure-brongroep en alle andere instellingen die u wilt gebruiken voor implementatie, zoals een [parametersbestand](../azure-resource-manager/templates/parameter-files.md) dat u moet gebruiken voor het doorgeven van sjabloonparameterwaarden, en selecteer **vervolgens Implementeren**.

   ![Implementatie-instellingen bevestigen](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Als het vak **Parameters bewerken** wordt weergegeven, geeft u de naam op voor uw logische app-bron in Azure en selecteert u **Opslaan**.  

   ![Implementatieparameters bewerken](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Wanneer de implementatie wordt gestart, wordt de status van de implementatie van uw app weergegeven in het **Uitvoer**-venster van Visual Studio. Als de status niet wordt weergegeven, opent u de lijst **Uitvoer tonen van** en selecteert u uw Azure-resourcegroep. Bijvoorbeeld:

   ![Uitvoervenster](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Als er verbindingen in uw logica-app van u moeten worden ingevoerd, wordt er een PowerShell-venster op de achtergrond geopend en wordt gevraagd om de benodigde wachtwoorden of geheime sleutels. Nadat u deze gegevens hebt ingevoerd, wordt de implementatie voortgezet.

   ![Verbindingen verifiëren](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Nadat de implementatie is voltooid, wordt uw logische app gepubliceerd, maar wordt deze niet geactiveerd in de Azure-portal.

1. Wanneer u klaar bent om uw logische app te activeren in de Azure-portal, zoekt en opent u uw logische app in de Logic App Designer. Selecteer **overzicht**in het menu van de logische app en selecteer **Inschakelen**.

1. Als u dubbele werkstromen wilt voorkomen, moet u ervoor zorgen dat u de oorspronkelijke stroom deactiveert of verwijdert.

Zie [Snelstart: Geautomatiseerde taken, processen en werkstromen maken met Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure) voor meer informatie over deze implementatiestappen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Connectors voor Azure Logic Apps](../connectors/apis-list.md)
* Meer informatie over [Azure Logic Apps](../logic-apps/logic-apps-overview.md)
