---
title: Taakwerkstromen automatiseren in Visual Studio
description: Terugkerende werkstromen voor bedrijfsintegratie maken, plannen en uitvoeren met Azure Logic Apps en Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.openlocfilehash: 693b2fd8ac7440b67f53de0aedb9a8268a90de76
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241652"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Snelstart: geautomatiseerde taken, processen en werkstromen maken met Azure Logic Apps - Visual Studio

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en Visual Studio kunt u werkstromen maken voor het automatiseren van taken en processen om apps, gegevens, systemen en services van bedrijven en organisaties te integreren. Deze quickstart laat zien hoe u deze werkstromen ontwerpen en bouwen door logische apps te maken in Visual Studio en deze apps te implementeren in Azure. Hoewel u deze taken uitvoeren in de Azure-portal, u met Visual Studio uw logische apps toevoegen aan bronbeheer, verschillende versies publiceren en Azure Resource Manager-sjablonen maken voor verschillende implementatieomgevingen.

Als u nieuw bent in Azure Logic Apps en alleen de basisconcepten wilt, probeert u de [snelle start voor het maken van een logische app in de Azure-portal.](../logic-apps/quickstart-create-first-logic-app-workflow.md) De Logic App Designer werkt op dezelfde manier in zowel de Azure-portal als Visual Studio.

In deze quickstart maakt u dezelfde logische app met Visual Studio als de Azure-portal snelstart. Deze logische app controleert de RSS-feed van een website en verzendt e-mail voor elk nieuw item in die feed. Uw voltooide logica-app ziet eruit als deze werkstroom op hoog niveau:

![Voltooide logische app](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Download en installeer deze hulpprogramma's als u ze nog niet hebt:

  * [Visual Studio 2019, 2017 of 2015 - Community-editie of meer](https://aka.ms/download-visual-studio). Deze quickstart maakt gebruik van Visual Studio Community 2017.

    > [!IMPORTANT]
    > Wanneer u Visual Studio 2019 of 2017 installeert, moet u de **Azure-ontwikkelingswerkbelasting** selecteren.

  * [Microsoft Azure SDK voor .NET (2.9.1 of hoger)](https://azure.microsoft.com/downloads/). Meer informatie over [Azure SDK voor .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * De nieuwste Azure Logic Apps-hulpprogramma's voor de Visual Studio-extensie voor de gewenste versie:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    U kunt hulpprogramma's van Azure Logic Apps ofwel rechtstreeks vanuit Visual Studio Marketplace downloaden en installeren, of leer [deze extensie te installeren vanuit Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Zorg ervoor dat u Visual Studio opnieuw opstart na de installatie.

* Toegang tot het web tijdens het gebruik van de ingebouwde ontwerpfunctie van Logic App

  De ontwerper heeft een internetverbinding nodig om resources in Azure te maken en eigenschappen en gegevens van connectors in uw logische app te lezen. Voor Dynamics CRM Online-verbindingen controleert de ontwerper bijvoorbeeld uw CRM-exemplaar op standaard- en aangepaste eigenschappen.

* Een e-mailaccount dat door Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Voor andere providers, bekijk de [connectors lijst hier](https://docs.microsoft.com/connectors/). In dit voorbeeld wordt Office 365 Outlook gebruikt. Als u een ander e-mailaccount gebruikt, zijn de algemene stappen hetzelfde, maar ziet de gebruikersinterface er misschien iets anders uit.

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Een Azure-resourcegroepproject maken

Om te beginnen, maakt u een [Azure-resourcegroepproject](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Meer informatie over [Azure-resourcegroepen en resources](../azure-resource-manager/management/overview.md).

1. Start Visual Studio. Meld u aan met uw Azure-account.

1. In het menu **File** selecteert u **New** > **Project**. (Toetsenbord: Ctrl + Shift + N)

   ![In het menu File selecteert u New > Project](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. Onder **Installed**, selecteert u **Visual C#** of **Visual Basic**. Selecteer **Cloud** > **Azure Resource Group**. Geef uw project een naam, bijvoorbeeld:

   ![Een Azure-resourcegroepproject maken](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Namen van resourcegroepen kunnen alleen letters, cijfers, perioden`.`(`-`), underscores`(`( `)``_`), koppeltekens ( )`.`en haakjes ( , ), maar kan niet *eindigen* met perioden ( ).
   >
   > Als **Cloud** of **Azure Resource Group** niet wordt weergegeven, moet u de Azure SDK voor Visual Studio installeren.

   Als u Visual Studio 2019 gebruikt, voert u de volgende stappen uit:

   1. Selecteer **in het** vak Een nieuw project maken het azure **resourcegroepproject** voor Visual C# of Visual Basic. Selecteer **Volgende**.

   1. Geef een naam op voor de Azure-brongroep die u wilt gebruiken en andere projectgegevens. Selecteer **Maken**.

1. Selecteer in de lijst met sjabloon de sjabloon **Logic App.** Selecteer **OK**.

   ![Sjabloon voor Logic App kiezen](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Nadat Visual Studio het project maakt, wordt Solution Explorer geopend en ziet u uw oplossing. In uw oplossing slaat het **LogicApp.json-bestand** niet alleen de definitie van uw logische app op, maar is het ook een Azure Resource Manager-sjabloon die u gebruiken voor implementatie.

   ![Solution Explorer toont de nieuwe logische app-oplossing en het implementatie-bestand](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Een lege, logische app maken

Wanneer u uw Azure Resource Group-project hebt, maakt u uw logische app met de sjabloon **Blank Logic App.**

1. Open in Solution Explorer het snelmenu van het beveiligingsbestand.json.In Solution Explorer open u het snelmenu van het bestand **LogicApp.json.** Selecteer **Openen met Logic App-ontwerpfunctie**. (Toetsenbord: Ctrl + L)

   ![Open het .json-bestand van de logische app met de Logic App-ontwerpfunctie](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Als je deze opdracht niet hebt in Visual Studio 2019, controleer dan of je de nieuwste updates voor Visual Studio hebt.

   Visual Studio vraagt u om uw Azure-abonnement en een Azure-brongroep voor het maken en implementeren van resources voor uw logische app en verbindingen.

1. Selecteer uw Azure-abonnement voor **Abonnement.** Selecteer **Voor resourcegroep** **Nieuw maken** om een andere Azure-brongroep te maken.

   ![Azure-abonnement, resourcegroep en resourcelocatie selecteren](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Instelling | Voorbeeldwaarde | Beschrijving |
   | ------- | ------------- | ----------- |
   | Gebruikersaccount | Fabrikam <br> sophia-owen@fabrikam.com | Het account dat u hebt gebruikt toen u zich aanmeldde bij Visual Studio |
   | **Abonnement** | Betalen per gebruik <br> (sophia-owen@fabrikam.com) | De naam voor uw Azure-abonnement en het bijbehorende account |
   | **Resourcegroep** | MyLogicApp-RG <br> (VS - west) | De Azure-brongroep en -locatie voor het opslaan en implementeren van de resources van uw logische app |
   | **Locatie** | **Hetzelfde als resourcegroep** | Het locatietype en de specifieke locatie voor het implementeren van uw logische app. Het locatietype is een Azure-regio of een bestaande [integratieserviceomgeving (ISE).](connect-virtual-network-vnet-isolated-environment.md) <p>Houd hiervoor het locatietype ingesteld op **Regio** en de locatie die is ingesteld op **Hetzelfde als Resourcegroep**. <p>**Opmerking:** Nadat u uw resourcegroepproject hebt gemaakt, u [het locatietype en de locatie wijzigen,](manage-logic-apps-with-visual-studio.md#change-location)maar een ander locatietype heeft op verschillende manieren invloed op uw logische app. |
   ||||

1. De Logic Apps Designer opent een pagina met een introductievideo en veelgebruikte triggers. Schuif omlaag langs de video en triggers naar **Sjablonen**en selecteer **Blank Logic App**.

   ![Selecteer Lege logische app](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Een werkstroom voor uw logische app compileren

Voeg vervolgens een [RSS-trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die wordt geactiveerd wanneer een nieuw feeditem wordt weergegeven. Elke logische app begint met een trigger, die wordt geactiveerd wanneer aan specifieke criteria wordt voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-engine een instantie van een logische app die de werkstroom uitvoert.

1. Selecteer in Logic App Designer onder het zoekvak de optie **Alles**. Voer in het zoekvak 'rss' in. Selecteer deze trigger in de lijst triggers: **Wanneer een feeditem wordt gepubliceerd**

   ![Uw logische app bouwen door toevoeging van een trigger en acties](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Nadat de trigger in de ontwerper is weergegeven, voltooit u het bouwen van de logische app door de werkstroomstappen in de [Azure-portal snel te volgen](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)en vervolgens terug te keren naar dit artikel. Wanneer u klaar bent, ziet uw logische app eruit zoals in dit voorbeeld:

   ![Voltooide logische app](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Sla uw Visual Studio-oplossing op. (toetsenbord: Ctrl + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Logische app implementeren in Azure

Voordat u uw logische app uitvoeren en testen, implementeert u de app vanuit Visual Studio naar Azure.

1. Selecteer in Solution Explorer in het snelmenu van uw project de optie**Nieuw** **implementeren** > . Meld u aan met uw Azure-account als u daarom wordt gevraagd.

   ![Een logische app-implementatie maken](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Houd voor deze implementatie het standaard Azure-abonnement, de brongroep en andere instellingen. Selecteer **Implementeren**.

   ![Logische app implementeren naar een Azure-resourcegroep](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Als het vak **Parameters bewerken** wordt weergegeven, geeft u een bronnaam op voor uw logische app. Sla uw wijzigingen op.

   ![Naam van de implementatie opgeven voor de logische app](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Wanneer de implementatie wordt gestart, wordt de status van de implementatie van uw app weergegeven in het **Uitvoer**-venster van Visual Studio. Als de status niet wordt weergegeven, opent u de lijst **Uitvoer tonen van** en selecteert u uw Azure-resourcegroep.

   ![Uitvoer implementatiestatus](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Als de geselecteerde connectors invoer van u nodig hebben, wordt er een PowerShell-venster op de achtergrond geopend en wordt gevraagd om de benodigde wachtwoorden of geheime sleutels. Nadat u deze gegevens hebt ingevoerd, wordt de implementatie voortgezet.

   ![PowerShell-venster](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Nadat de implementatie is voltooid, is uw logische app live in de Azure-portal en wordt uitgevoerd volgens uw opgegeven planning (elke minuut). Als de trigger nieuwe feeditems vindt, wordt de trigger geactiveerd, waardoor een werkstroominstantie wordt gemaakt waarmee de acties van uw logische app worden uitgevoerd. Uw logische app verzendt e-mail voor elk nieuw item. Als de trigger anders geen nieuwe items vindt, wordt de trigger niet geactiveerd en wordt de werkstroom niet geanimeerd. Uw logische app wacht tot het volgende interval voordat u het controleert.

   Hier zijn voorbeelde-mails die deze logische app verzendt. Als u geen een e-mailberichten ontvangt, controleert u de map Ongewenste e-mail.

   ![Outlook verzendt een e-mail voor elk nieuw RSS-item](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Gefeliciteerd, u hebt uw logische app met succes gebouwd en geïmplementeerd met Visual Studio. Zie [Logische apps beheren met Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) om uw logische app te beheren en de uitvoeringsgeschiedenis te inspecteren.

## <a name="add-new-logic-app"></a>Nieuwe logische app toevoegen

Wanneer u een bestaand Azure Resource Group-project hebt, u een nieuwe lege logische app aan dat project toevoegen met behulp van het JSON-overzichtsvenster.

1. Open het `<logic-app-name>.json` bestand in Solution Explorer.

1. Selecteer **in het** menu Weergave de optie **Andere Windows** > **JSON-overzicht**.

1. Als u een bron aan het sjabloonbestand wilt toevoegen, selecteert u **Resource toevoegen** boven aan het venster JSON-overzicht. Of open in het venster JSON-overzicht het snelmenu **resources** en selecteer **Nieuwe bron toevoegen**.

   ![Venster JSON-overzicht](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. Zoek in het dialoogvenster **Resource toevoegen** in `logic app`het zoekvak op logische app en selecteer **deze .** Geef uw logische app een naam en selecteer **Toevoegen**.

   ![Bron toevoegen](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met uw logische app, verwijdert u de brongroep die uw logische app en gerelateerde bronnen bevat.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met hetzelfde account dat is gebruikt voor het maken van uw logische app.

1. Selecteer **resourcegroepen**in het menu Van De Azure-portal of zoek naar **resourcegroepen** op een pagina en selecteer deze. Selecteer de brongroep van uw logische app.

1. Selecteer **op** de pagina Overzicht de optie **Brongroep verwijderen**. Voer de naam van de brongroep in als bevestiging en selecteer **Verwijderen**.

   !['Resourcegroepen' > 'Overzicht' > 'Resourcegroep verwijderen'](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Verwijder de Visual Studio-oplossing van uw lokale computer.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u uw logische app met Visual Studio gebouwd, geïmplementeerd en uitgevoerd. Zie de volgende artikelen voor meer informatie over het beheren en uitvoeren van geavanceerde implementatie voor logische apps met Visual Studio:

> [!div class="nextstepaction"]
> [Logische apps beheren met Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
