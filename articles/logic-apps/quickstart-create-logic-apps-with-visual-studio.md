---
title: Taak stromen automatiseren in Visual Studio
description: Maak, plan en voer terugkerende werk stromen voor bedrijfs integratie uit met behulp van Azure Logic Apps en Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.openlocfilehash: 3311d1143c0eca0c2b57e89e98dc22ab14fd9308
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147727"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Snelstartgids: geautomatiseerde taken, processen en werk stromen maken met Azure Logic Apps-Visual Studio

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en Visual Studio kunt u werkstromen maken voor het automatiseren van taken en processen om apps, gegevens, systemen en services van bedrijven en organisaties te integreren. Deze Quick Start laat zien hoe u deze werk stromen kunt ontwerpen en bouwen door Logic apps te maken in Visual Studio en deze apps te implementeren in Azure. Hoewel u deze taken kunt uitvoeren in de Azure Portal, kunt u met Visual Studio uw Logic apps toevoegen aan broncode beheer, verschillende versies publiceren en Azure Resource Manager sjablonen maken voor verschillende implementatie omgevingen.

Als u niet bekend bent met Azure Logic Apps en alleen de basis concepten wilt, probeert u de [Snelstartgids voor het maken van een logische app in de Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). De ontwerp functie voor logische apps werkt op vergelijk bare wijze in zowel de Azure Portal als Visual Studio.

In deze Quick Start maakt u dezelfde logische app met Visual Studio als Azure Portal Quick Start. Deze logische app bewaakt de RSS-feed van een website en verzendt e-mail voor elk nieuw item in die feed. Uw voltooide logische app ziet eruit als deze werk stroom op hoog niveau:

![Voltooide logische app](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Download en installeer deze hulpprogramma's als u ze nog niet hebt:

  * [Visual Studio 2019, 2017 of 2015-Community Edition of hoger](https://aka.ms/download-visual-studio). Deze Snelstartgids maakt gebruik van Visual Studio Community 2017.

    > [!IMPORTANT]
    > Wanneer u Visual Studio 2019 of 2017 installeert, moet u ervoor zorgen dat u de **Azure Development** -werk belasting selecteert.

  * [Microsoft Azure SDK voor .net (2.9.1 of hoger)](https://azure.microsoft.com/downloads/). Meer informatie over [Azure SDK voor .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * De nieuwste Azure Logic Apps-Hulpprogram Ma's voor de Visual Studio-extensie voor de gewenste versie:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    U kunt hulpprogramma's van Azure Logic Apps ofwel rechtstreeks vanuit Visual Studio Marketplace downloaden en installeren, of leer [deze extensie te installeren vanuit Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Zorg ervoor dat u Visual Studio opnieuw opstart na de installatie.

* Toegang tot het web tijdens het gebruik van de ingebouwde ontwerpfunctie van Logic App

  De ontwerp functie heeft een Internet verbinding nodig om resources te maken in Azure en om eigenschappen en gegevens van connectors in uw logische app te lezen. Voor bijvoorbeeld Dynamics CRM Online-verbindingen controleert de ontwerper uw CRM-exemplaar op standaard-en aangepaste eigenschappen.

* Een e-mailaccount dat door Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Raadpleeg de [lijst met connectors hier](https://docs.microsoft.com/connectors/)voor andere providers. In dit voor beeld wordt Office 365 Outlook gebruikt. Als u een ander e-mailaccount gebruikt, zijn de algemene stappen hetzelfde, maar ziet de gebruikersinterface er misschien iets anders uit.

  > [!IMPORTANT]
  > Als u de Gmail-connector wilt gebruiken, kunnen alleen zakelijke accounts van G-Suite deze connector gebruiken zonder beperkingen in Logic apps. Als u een Gmail-Consumer-account hebt, kunt u deze connector gebruiken met alleen specifieke door Google goedgekeurde Services, of u kunt [een Google-client-app maken die voor verificatie met uw Gmail-connector wordt gebruikt](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Zie voor meer informatie [beleid voor gegevens beveiliging en privacybeleid voor Google connectors in azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Een Azure-resourcegroepproject maken

Om te beginnen, maakt u een [Azure-resourcegroepproject](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Meer informatie over [Azure-resourcegroepen en resources](../azure-resource-manager/management/overview.md).

1. Start Visual Studio. Meld u aan met uw Azure-account.

1. In het menu **File** selecteert u **New** > **Project**. (Toetsen bord: CTRL + SHIFT + N)

   ![In het menu File selecteert u New > Project](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. Onder **Installed**, selecteert u **Visual C#** of **Visual Basic**. Selecteer **Cloud** > **Azure-resource groep**. Geef uw project een naam, bijvoorbeeld:

   ![Een Azure-resourcegroepproject maken](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Namen van resource groepen mogen alleen letters, cijfers, punten (`.`), onderstrepings tekens (`_`), afbreek streepjes`-`() en haakjes (`(`, `)`) bevatten, maar mogen niet *eindigen* op`.`een punt ().
   >
   > Als de **Cloud** of **Azure-resource groep** niet wordt weer gegeven, moet u de Azure SDK voor Visual Studio installeren.

   Als u Visual Studio 2019 gebruikt, voert u de volgende stappen uit:

   1. Selecteer in het vak **een nieuw project maken** het **Azure-resource groeps** project voor Visual C# of Visual Basic. Selecteer **Next**.

   1. Geef een naam op voor de Azure-resource groep die u wilt gebruiken en andere project gegevens. Selecteer **Maken**.

1. Selecteer in de lijst sjabloon de sjabloon **Logic app** . Selecteer **OK**.

   ![Sjabloon voor Logic App kiezen](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Nadat Visual Studio het project maakt, wordt Solution Explorer geopend en ziet u uw oplossing. In uw oplossing slaat het bestand **LogicApp. json** niet alleen de definitie van de logische app op, maar is ook een Azure Resource Manager sjabloon die u voor implementatie kunt gebruiken.

   ![Solution Explorer toont de nieuwe logische app-oplossing en het implementatie-bestand](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Een lege, logische app maken

Wanneer u uw Azure-resource groep-project hebt, maakt u uw logische app met de sjabloon voor de **lege logische app** .

1. Open in Solution Explorer het snelmenu van het bestand **LogicApp. json** . Selecteer **Openen met Logic App-ontwerpfunctie**. (Toetsen bord: CTRL + L)

   ![Open het .json-bestand van de logische app met de Logic App-ontwerpfunctie](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Als u deze opdracht niet in Visual Studio 2019 hebt, controleert u of u de meest recente updates voor Visual Studio hebt.

   Visual Studio vraagt u om uw Azure-abonnement en een Azure-resource groep voor het maken en implementeren van resources voor uw logische app en verbindingen.

1. Selecteer uw Azure-abonnement voor het **abonnement**. Voor **resource groep**selecteert u **Nieuw maken** om een andere Azure-resource groep te maken.

   ![Azure-abonnement, resourcegroep en resourcelocatie selecteren](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Instelling | Voorbeeldwaarde | Beschrijving |
   | ------- | ------------- | ----------- |
   | Gebruikers account | Fabrikam <br> sophia-owen@fabrikam.com | Het account dat u hebt gebruikt bij het aanmelden bij Visual Studio |
   | **Abonnement** | Betalen per gebruik <br> (sophia-owen@fabrikam.com) | De naam voor uw Azure-abonnement en het bijbehorende account |
   | **Resource groep** | MyLogicApp-RG <br> (VS - west) | De Azure-resource groep en-locatie voor het opslaan en implementeren van de resources van uw logische app |
   | **Locatie** | **Hetzelfde als resource groep** | Het locatie type en de specifieke locatie voor het implementeren van uw logische app. Het locatie type is ofwel een Azure-regio ofwel een bestaande [integratie service omgeving (ISE)](connect-virtual-network-vnet-isolated-environment.md). <p>Voor deze Quick Start laat u het locatie type ingesteld op **regio** en de locatie ingesteld op **hetzelfde als de resource groep**. <p>**Opmerking**: nadat u het project voor de resource groep hebt gemaakt, kunt u [het locatie type en de locatie wijzigen](manage-logic-apps-with-visual-studio.md#change-location), maar een ander locatie type is op verschillende manieren van invloed op uw logische app. |
   ||||

1. De Logic Apps Designer opent een pagina met een introductie video en veelgebruikte triggers. Schuif omlaag in de video en activeer de **sjablonen**en selecteer **lege logische app**.

   ![Selecteer Lege logische app](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Een werkstroom voor uw logische app compileren

Voeg vervolgens een RSS- [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die wordt geactiveerd wanneer een nieuw feed-item wordt weer gegeven. Elke logische app begint met een trigger, die wordt geactiveerd wanneer aan specifieke criteria wordt voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-engine een instantie van een logische app die de werkstroom uitvoert.

1. Selecteer in de ontwerp functie voor logische apps, onder het zoekvak, de optie **alle**. Typ ' RSS ' in het zoekvak. Selecteer in de lijst triggers deze trigger: **Wanneer een item in de feed wordt gepubliceerd**

   ![Uw logische app bouwen door toevoeging van een trigger en acties](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Nadat de trigger wordt weer gegeven in de ontwerp functie, voltooit u de logische app met de werk stroom stappen in de [Snelstartgids van Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)en gaat u terug naar dit artikel. Wanneer u klaar bent, ziet uw logische app eruit zoals in dit voorbeeld:

   ![Voltooide logische app](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Sla uw Visual Studio-oplossing op. (toetsenbord: Ctrl + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Logische app implementeren in Azure

Voordat u uw logische app kunt uitvoeren en testen, implementeert u de app in azure vanuit Visual Studio.

1. Selecteer in Solution Explorer in het snelmenu van het project de optie **Deploy** > **nieuwe**implementeren. Meld u aan met uw Azure-account als u daarom wordt gevraagd.

   ![Een logische app-implementatie maken](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Voor deze implementatie moet u het standaard Azure-abonnement, de resource groep en andere instellingen blijven gebruiken. Selecteer **Implementeren**.

   ![Logische app implementeren naar een Azure-resourcegroep](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Als het vak **para meters bewerken** wordt weer gegeven, geeft u een resource naam op voor uw logische app. Sla uw wijzigingen op.

   ![Naam van de implementatie opgeven voor de logische app](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Wanneer de implementatie wordt gestart, wordt de status van de implementatie van uw app weergegeven in het **Uitvoer**-venster van Visual Studio. Als de status niet wordt weergegeven, opent u de lijst **Uitvoer tonen van** en selecteert u uw Azure-resourcegroep.

   ![Uitvoer implementatiestatus](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Als uw geselecteerde connectors invoer nodig hebben, wordt er een Power shell-venster geopend op de achtergrond en wordt u gevraagd om de benodigde wacht woorden of geheime sleutels. Nadat u deze gegevens hebt ingevoerd, wordt de implementatie voortgezet.

   ![Power shell-venster](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Nadat de implementatie is voltooid, is de logische app in de Azure Portal live en wordt deze uitgevoerd op uw opgegeven planning (elke minuut). Als de trigger nieuwe feed-items vindt, wordt de trigger geactiveerd, waarmee een werk stroom exemplaar wordt gemaakt waarmee de acties van uw logische app worden uitgevoerd. Uw logische app verzendt e-mail voor elk nieuw item. Als de trigger geen nieuwe items kan vinden, wordt de trigger niet geactiveerd en wordt de werk stroom niet meer door "overs Laan" verzonden. Uw logische app wacht tot het volgende interval voor de controle.

   Hier vindt u voor beelden van e-mail berichten die deze logische app verzendt. Als u geen een e-mailberichten ontvangt, controleert u de map Ongewenste e-mail.

   ![Outlook verzendt een e-mail voor elk nieuw RSS-item](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Gefeliciteerd, u hebt uw logische app gemaakt en geïmplementeerd met Visual Studio. Zie [Logische apps beheren met Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) om uw logische app te beheren en de uitvoeringsgeschiedenis te inspecteren.

## <a name="add-new-logic-app"></a>Nieuwe logische app toevoegen

Wanneer u een bestaand project van een Azure-resource groep hebt, kunt u een nieuwe, lege logische app toevoegen aan dat project met behulp van het JSON-overzichts venster.

1. Open het `<logic-app-name>.json` bestand in Solution Explorer.

1. Selecteer in het menu **weer gave** **andere** > **JSON-overzicht**van Windows.

1. Als u een resource wilt toevoegen aan het sjabloon bestand, selecteert u **resource toevoegen** boven aan het venster JSON-overzicht. U kunt ook in het venster JSON-overzicht het snelmenu **resources** openen en **nieuwe resource toevoegen**selecteren.

   ![Venster JSON-overzicht](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. In het dialoog venster **resource toevoegen** in het zoekvak zoeken `logic app`en selecteert u **logische apps**. Geef de logische app een naam en selecteer **toevoegen**.

   ![Resource toevoegen](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met uw logische app, verwijdert u de resource groep die uw logische app en gerelateerde resources bevat.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met hetzelfde account dat is gebruikt voor het maken van uw logische app.

1. Selecteer **resource groepen**in het menu Azure portal of zoek en selecteer **resource groepen** op een wille keurige pagina. Selecteer de resource groep van de logische app.

1. Selecteer op de pagina **overzicht** de optie **resource groep verwijderen**. Voer de naam van de resource groep in als bevestiging en selecteer **verwijderen**.

   !['Resourcegroepen' > 'Overzicht' > 'Resourcegroep verwijderen'](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Verwijder de Visual Studio-oplossing van uw lokale computer.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u uw logische app met Visual Studio gebouwd, geïmplementeerd en uitgevoerd. Raadpleeg de volgende artikelen voor meer informatie over het beheren en uitvoeren van geavanceerde implementatie voor Logic apps met Visual Studio:

> [!div class="nextstepaction"]
> [Logische apps beheren met Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
