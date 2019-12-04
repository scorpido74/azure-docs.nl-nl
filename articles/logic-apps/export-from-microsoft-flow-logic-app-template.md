---
title: Stromen exporteren van automatische stroom naar Azure Logic Apps
description: Migreer stromen van automatische stroom naar Azure Logic Apps door te exporteren als Azure Resource Manager sjablonen
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 4512fd8a236c888937eb60a75d01cfb0ef2c1913
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792642"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Stromen exporteren vanuit Power Automate en implementeren in Azure Logic Apps

Om de mogelijkheden van uw stroom uit te breiden en uit te breiden, kunt u die stroom migreren van [Automatische stroom](https://flow.microsoft.com) naar [Azure Logic apps](../logic-apps/logic-apps-overview.md). U kunt uw stroom exporteren als een Azure Resource Manager sjabloon voor een logische app, de sjabloon Logic app implementeren naar een Azure-resource groep en die logische app vervolgens openen in de ontwerp functie voor logische apps.

> [!NOTE]
> Niet alle stroom verautomatiseerde connectors zijn beschikbaar in Azure Logic Apps. U kunt stromen met [gelijkwaardige connectors](../connectors/apis-list.md) importeren in azure Logic apps. De knop trigger, de goedkeurings connector en de notification connector zijn bijvoorbeeld specifiek voor het automatiseren van het energie verbruik.
>
> OpenAPI stromen die zijn geëxporteerd uit het automatiseren van de stroom worden momenteel niet ondersteund voor implementatie als sjablonen voor logische apps. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De stroom die u wilt exporteren uit automatische energie

## <a name="export-a-flow"></a>Een stroom exporteren

1. Meld u aan bij [energie automatisering](https://flow.microsoft.com)en selecteer **mijn stromen**. Zoek en selecteer uw stroom. Selecteer op de werk balk de knop met weglatings tekens ( **...** ). Selecteer > **Logic apps sjabloon (. json)** **exporteren** .

   ![Stroom exporteren](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Sla uw sjabloon op naar de gewenste locatie.

Zie [groeien tot Azure Logic apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)voor meer informatie.

## <a name="deploy-template-by-using-the-azure-portal"></a>Sjabloon implementeren met behulp van de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw Azure-account.

1. Selecteer **Een resource maken** in het hoofdmenu van Azure. Voer in het zoekvak ' sjabloon implementatie ' in. Selecteer **Sjabloonimlementatie (Implementeer met aangepaste sjablonen)** en selecteer vervolgens **maken**.

   ![Selecteer Sjabloonimlementatie](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Onder **aangepaste implementatie**selecteert u **uw eigen sjabloon bouwen in de editor**.

   ![Selecteer ' uw eigen sjabloon bouwen in de editor '](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Selecteer in de werk balk **sjabloon bewerken** de optie **bestand laden**. Zoek en selecteer de JSON-sjabloon die u hebt geëxporteerd uit automatische stroom en selecteer **openen**.

   ![Selecteer bestand laden](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Selecteer **Opslaan**nadat de JSON, para meters en resources in uw sjabloon zijn weer gegeven in de editor.
  
   ![Sjabloon opslaan](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Geef nu de volgende invoer parameters op voor de sjabloon:

   * Azure-abonnement dat moet worden gebruikt voor facturering
   * Azure-resourcegroep
   * Locatie voor de Azure-resource groep
   * Naam voor de logische app-resource
   * Locatie voor de logische app-resource, als deze afwijkt van de Azure-resource groep
   * De naam voor eerder gemaakte verbindingen die de logische app kan hergebruiken

      Als u uw eerste logische app maakt, worden alle verbindingen als nieuw gemaakt, zodat u de standaard namen kunt accepteren. Anders kunt u de namen opgeven voor eerder gemaakte verbindingen, die u kunt gebruiken in meerdere logische apps.

   Nadat u deze informatie hebt opgegeven voor de sjabloon, controleert en gaat u akkoord met de Azure Marketplace-voor waarden voor het maken van de benodigde Azure-resources en het factureren van uw Azure-abonnement, en selecteert u vervolgens **aanschaffen**.
  
   ![Invoer parameters voor sjabloon opgeven](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure implementeert uw sjabloon als een logische app voor uw opgegeven resource groep. Alle Logic apps die u migreert vanuit automatische energie, worden geïmplementeerd met een uitgeschakelde status.

1. Voordat u uw logische app activeert, moet u nieuwe verbindingen toestaan door de volgende stappen uit te voeren:

   1. Open de logische app die u hebt gemaakt. Selecteer in het menu van de logische app de optie **Logic app Designer**.

      Voor elke verbinding waarvoor autorisatie is vereist, wordt een waarschuwings pictogram weer gegeven:

      ![Waarschuwings pictogram](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Voor elke stap waarvoor een geautoriseerde verbinding is vereist, vouwt u die stap uit en selecteert u **nieuwe toevoegen**.

      ![Nieuwe verbinding toevoegen](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Meld u aan bij elke service of geef de benodigde referenties op om de verbinding te autoriseren.

1. Sla uw logische app op. Wanneer u klaar bent om uw logische app te activeren, selecteert u **overzicht**in het menu van de logische app en selecteert u vervolgens **inschakelen**.

   ![Logische app inschakelen](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Om te voor komen dat dubbele werk stromen worden uitgevoerd, moet u ervoor zorgen dat u de oorspronkelijke stroom deactiveert of verwijdert.

## <a name="deploy-template-by-using-visual-studio"></a>Een sjabloon implementeren met behulp van Visual Studio

Als u Visual Studio hebt ingesteld met de [vereisten](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) voor het maken van Logic apps, kunt u de geëxporteerde sjabloon vanuit Visual Studio implementeren naar Azure Logic apps.

1. Open in Visual Studio het sjabloon bestand dat u hebt geëxporteerd uit automatische stroom automatisering.

1. Maak in Visual Studio een Azure-resource groep-project en selecteer de sjabloon voor **logische apps** door de stappen te volgen in [Quick Start: Maak geautomatiseerde taken, processen en werk stromen met Azure Logic apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md), bijvoorbeeld:

   ![Een Azure-resourcegroepproject maken](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Open vanuit Solution Explorer het bestand **LogicApp. json** als het bestand nog niet is geopend.

1. Kopieer de inhoud van de geëxporteerde sjabloon en vervang de inhoud in het bestand **LogicApp. json** .

1. Voordat u uw logische app implementeert, moet u de volgende stappen uitvoeren om nieuwe verbindingen te autoriseren:

   1. Open het **LogicApp. json** -snelmenu en selecteer vervolgens **openen met Logic app Designer**.

      ![Sjabloon openen met Logic app Designer](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Als u hierom wordt gevraagd, selecteert u het Azure-abonnement en de resource groep die u wilt gebruiken voor het implementeren van uw logische app.

      ![Azure-abonnement en-resource groep selecteren](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Nadat de logische app in de ontwerp functie wordt weer gegeven, geven verbindingen waarvoor autorisatie waarschuwings pictogrammen moeten worden weer gegeven:

      ![Verbindingen met waarschuwings pictogrammen](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Voor elke stap waarvoor een geautoriseerde verbinding is vereist, vouwt u die stap uit en selecteert u **nieuwe toevoegen**.

      ![Nieuwe verbinding toevoegen](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Meld u aan bij elke service of geef de benodigde referenties op om de verbinding te autoriseren.

   1. Sla uw oplossing op voordat u de logische app implementeert.

1. Open in Solution Explorer het snelmenu project en selecteer > **nieuwe** **implementeren** . Meld u aan met uw Azure-account als u daarom wordt gevraagd.

1. Wanneer u hierom wordt gevraagd, bevestigt u het Azure-abonnement, de Azure-resource groep en alle andere instellingen die u wilt gebruiken voor implementatie, zoals een [parameter bestand](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) dat moet worden gebruikt voor het door geven van sjabloon parameter waarden, en selecteer vervolgens **implementeren**.

   ![Implementatie-instellingen bevestigen](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Als het vak **para meters bewerken** wordt weer gegeven, geeft u de naam op voor de logische app-resource in Azure en selecteert u **Opslaan**.  

   ![Implementatie parameters bewerken](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Wanneer de implementatie wordt gestart, wordt de status van de implementatie van uw app weergegeven in het **Uitvoer**-venster van Visual Studio. Als de status niet wordt weergegeven, opent u de lijst **Uitvoer tonen van** en selecteert u uw Azure-resourcegroep. Bijvoorbeeld:

   ![Uitvoervenster](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Als er verbindingen in uw logische app moeten worden ingevoerd, wordt er een Power shell-venster geopend op de achtergrond en wordt u gevraagd om de benodigde wacht woorden of geheime sleutels. Nadat u deze gegevens hebt ingevoerd, wordt de implementatie voortgezet.

   ![Verbindingen verifiëren](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Nadat de implementatie is voltooid, wordt uw logische app gepubliceerd, maar wordt deze niet geactiveerd in de Azure Portal.

1. Wanneer u klaar bent om uw logische app te activeren in de Azure Portal, kunt u uw logische app zoeken en openen in de ontwerp functie voor logische apps. Selecteer **overzicht**in het menu van de logische app en selecteer vervolgens **inschakelen**.

1. Om te voor komen dat dubbele werk stromen worden uitgevoerd, moet u ervoor zorgen dat u de oorspronkelijke stroom deactiveert of verwijdert.

Voor meer informatie over deze implementaties tappen raadpleegt u [Quick Start: geautomatiseerde taken, processen en werk stromen maken met Azure Logic apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [connectors voor Azure Logic apps](../connectors/apis-list.md)
* Meer informatie over [Azure Logic apps](../logic-apps/logic-apps-overview.md)
