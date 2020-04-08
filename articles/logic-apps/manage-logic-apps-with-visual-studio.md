---
title: Logische apps bewerken en beheren met Visual Studio met Cloud Explorer
description: Logische apps bewerken, bijwerken, beheren, toevoegen aan bronbeheer en implementeren met Visual Studio met Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: 73df5b7f10e038b6894996eb83dec7b6914a4536
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803187"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Logische apps beheren met Visual Studio

Hoewel u logische apps maken, bewerken, beheren en implementeren in de [Azure-portal,](https://portal.azure.com)u Visual Studio ook gebruiken wanneer u uw logische apps wilt toevoegen aan bronbeheer, verschillende versies wilt publiceren en [Azure Resource Manager-sjablonen](../azure-resource-manager/management/overview.md) voor verschillende implementatieomgevingen wilt maken. Met Visual Studio Cloud Explorer u uw logische apps vinden en beheren, samen met andere Azure-bronnen. U bijvoorbeeld logische apps openen, downloaden, bewerken, uitvoeren, uitvoeren, geschiedenis weergeven, uitschakelen en logische apps inschakelen die al zijn geïmplementeerd in de Azure-portal. Als u nieuw bent in het werken met Azure Logic Apps in Visual Studio, leest u [hoe u logische apps maakt met Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

U uw logische apps ook [beheren in de Azure-portal.](manage-logic-apps-with-azure-portal.md)

> [!IMPORTANT]
> Door een logische app vanuit Visual Studio te implementeren of te publiceren, wordt de versie van die app in de Azure-portal overgeschreven. Als u dus wijzigingen aanbrengt in de Azure-portal die u wilt behouden, moet u ervoor zorgen dat u [de logische app in Visual Studio vernieuwt](#refresh) vanuit de Azure-portal voordat u de volgende keer implementeert of publiceert vanuit Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Download en installeer deze hulpprogramma's als u ze nog niet hebt:

  * [Visual Studio 2019, 2017 of 2015 - Community-editie of meer](https://aka.ms/download-visual-studio). Deze snelstart maakt gebruik van Visual Studio Community 2017, dit is gratis.

    > [!IMPORTANT]
    > Wanneer u Visual Studio 2019 of 2017 installeert, moet u de **Azure-ontwikkelingswerkbelasting** selecteren.
    > Zie [Resources beheren die zijn gekoppeld aan uw Azure-accounts in Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view)voor meer informatie.

    Als u Cloud Explorer voor Visual Studio 2015 wilt [installeren, downloadt u Cloud Explorer van de Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Zie [Resources beheren die zijn gekoppeld aan uw Azure-accounts in Visual Studio Cloud Explorer (2015) voor](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015)meer informatie.

  * [Azure SDK (2.9.1 of hoger)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * De nieuwste Azure Logic Apps-hulpprogramma's voor de Visual Studio-extensie voor de gewenste versie:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    U kunt hulpprogramma's van Azure Logic Apps ofwel rechtstreeks vanuit Visual Studio Marketplace downloaden en installeren, of leer [deze extensie te installeren vanuit Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Zorg ervoor dat u Visual Studio opnieuw opstart na de installatie.

* Toegang tot het web tijdens het gebruik van de ingesloten Logic Apps Designer

  De ontwerpfunctie moet over een internetverbinding beschikken om resources te maken in Azure en de eigenschappen en gegevens van connectoren in uw logische app te lezen. Als u bijvoorbeeld de Dynamics CRM Online-connector gebruikt, controleert de ontwerpfunctie uw CRM-exemplaar voor de beschikbare standaardregels en aangepaste eigenschappen.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Uw logische apps zoeken

In Visual Studio u alle logische apps vinden die zijn gekoppeld aan uw Azure-abonnement en worden geïmplementeerd in de Azure-portal met Behulp van Cloud Explorer.

1. Open Visual Studio. Selecteer **Cloud Explorer**in het menu **Weergave** .

1. Selecteer **accountbeheer**in Cloud Explorer . Selecteer het Azure-abonnement dat is gekoppeld aan uw logische apps en selecteer **Vervolgens Toepassen**. Bijvoorbeeld:

   ![Selecteer 'Accountbeheer'](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Voer de volgende stappen uit op basis van of u zoekt op **resourcegroepen** of **resourcetypen:**

   * **Resourcegroepen:** In Cloud Explorer worden onder uw Azure-abonnement alle resourcegroepen weergegeven die aan dat abonnement zijn gekoppeld. Vouw de resourcegroep uit die uw logische app bevat en selecteer vervolgens uw logische app.

   * **Resourcetypen:** Vouw onder uw Azure-abonnement **Logische apps**uit . Nadat in Cloud Explorer alle geïmplementeerde logische apps zijn weergegeven die aan uw abonnement zijn gekoppeld, selecteert u uw logische app.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Openen in Visual Studio

In Visual Studio u logische apps openen die eerder zijn gemaakt en geïmplementeerd, rechtstreeks via de Azure-portal of als Azure Resource Group-projecten met Visual Studio.

1. Open Cloud Explorer en zoek uw logische app.

1. Selecteer Openen met Logic App **Editor**in het snelmenu van de logische app .

   > [!TIP]
   > Als je deze opdracht niet hebt in Visual Studio 2019, controleer dan of je de nieuwste updates voor Visual Studio hebt.

   In dit voorbeeld worden logische apps weergegeven op resourcetype, zodat uw logische apps worden weergegeven onder de sectie **Logische apps.**

   ![Geïmplementeerde logische app openen vanuit Azure-portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Nadat de logische app is geopend in Logic Apps Designer, u onder aan de ontwerper **codeweergave** selecteren, zodat u de onderliggende definitiestructuur van de logische app controleren. Als u een implementatiesjabloon voor de logische app wilt maken, leest u hoe u een Sjabloon azure resource manager voor die logische app [downloadt.](#download-logic-app) Meer informatie over [Resource Manager-sjablonen](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Downloaden van Azure

U logische apps downloaden van de [Azure-portal](https://portal.azure.com) en deze opslaan als [Azure Resource Manager-sjablonen.](../azure-resource-manager/management/overview.md) U de sjablonen vervolgens lokaal bewerken met Visual Studio en logische apps aanpassen voor verschillende implementatieomgevingen.  Het downloaden van logische apps *parameteriseert* automatisch hun definities in [Resource Manager-sjablonen](../azure-resource-manager/templates/overview.md), die ook JavaScript Object Notatie (JSON) gebruiken.

1. Open Cloud Explorer in Visual Studio. Zoek en selecteer de logische app die u wilt downloaden van Azure.

1. Selecteer Openen **met Logic App Editor**in het snelmenu van die app .

   > [!TIP]
   > Als je deze opdracht niet hebt in Visual Studio 2019, controleer dan of je de nieuwste updates voor Visual Studio hebt.

   De Logic App Designer opent en toont de logische app. Als u de onderliggende definitie en structuur van de logische app wilt bekijken, selecteert u onder aan de ontwerper de **codeweergave**.

1. Selecteer op de werkbalk van de ontwerper de optie **Downloaden**.

   ![Logische app downloaden van Azure-portal](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Wanneer u om een locatie wordt gevraagd, bladert u naar die locatie en slaat u de sjabloon Resourcemanager op voor de definitie van de logische app in json-bestandsindeling (JSON).

   De definitie van uw `resources` logische app wordt weergegeven in de subsectie in de sjabloon Resourcemanager. U nu de logische app-definitie en resourcemanagersjabloon bewerken met Visual Studio. U de sjabloon ook toevoegen als een [Azure Resource Group-project](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) aan een Visual Studio-oplossing. Meer informatie over [Azure Resource Group-projecten voor logische apps in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Koppeling naar integratieaccount

Als u voor enterprise-integratiescenario's (logic apps for business-to-business) enterprise-integratiescenario's wilt bouwen, u uw logische app koppelen aan een eerder gemaakt [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat in dezelfde regio als uw logica-app bestaat. Een integratieaccount bevat B2B-artefacten, zoals handelspartners, overeenkomsten, schema's en kaarten, en laat uw logische app B2B-connectors gebruiken voor XML-validatie en platte bestandscodering of -decodering. Hoewel u deze koppeling [maken met behulp van de Azure-portal,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)u Visual Studio ook gebruiken nadat u aan de vereisten hebt [voldoen](#requirements)en uw logische app bestaat als een JSON-bestand (.json) in een Azure [Resource Group-project](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Meer informatie over [Azure Resource Group-projecten voor logische apps in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. Open in Visual Studio het azure resourcegroep-project dat uw logische app bevat.

1. Open in Solution Explorer het snelmenu **van<logic-app-naam>.json-bestand** en selecteer **Openen met Logic App Designer**. (Toetsenbord: Ctrl + L)

   ![Het Json-bestand van de logische app openen met Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Als u deze opdracht niet hebt in Visual Studio 2019, controleert u of u de nieuwste updates voor Visual Studio en de extensie Azure Logic Apps Tools hebt.

1. Zorg ervoor dat de Logic App Designer zich heeft gericht door het tabblad of het oppervlak van de ontwerper te selecteren, zodat in het venster Eigenschappen de eigenschap **Integratieaccount** voor uw logische app wordt weergegeven.

   ![Eigenschappenvenster - eigenschap 'Integratieaccount'](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Als het venster Eigenschappen nog niet is geopend, selecteert u in het menu **Weergave** de optie **Venster Eigenschappen**. (Toetsenbord: Druk op F4)

1. Open de eigenschappenlijst **voor integratieaccount** en selecteer het integratieaccount dat u wilt koppelen aan uw logische app, bijvoorbeeld:

   ![Eigenschappenlijst 'Integratieaccount' openen](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Als u klaar bent, moet u uw Visual Studio-oplossing opslaan.

Wanneer u de eigenschap **Integratieaccount** inVisual Studio instelt en uw logische app opslaat als een Azure Resource Manager-sjabloon, bevat die sjabloon ook een parameterdeclaratie voor het geselecteerde integratieaccount. Zie [Overzicht: Implementatie van logische apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)voor meer informatie over sjabloonparameters en logische apps.

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Implementatielocatie wijzigen

Als uw logische app in Visual Studio bestaat als een JSON-bestand (.json) binnen een [Azure Resource Group-project](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) dat u gebruikt om de implementatie te automatiseren, wordt die logische app ingesteld op een locatietype en een specifieke locatie. Deze locatie is een Azure-regio of een bestaande [integratieserviceomgeving (ISE).](connect-virtual-network-vnet-isolated-environment.md)

Als u het locatietype of de locatie van uw logische app wilt wijzigen, moet u het werkstroomdefinitiebestand (.json) van uw logische app openen vanuit Solution Explorer met behulp van de Logic App Designer. U deze eigenschappen niet wijzigen met Cloud Explorer.

> [!IMPORTANT]
> Het wijzigen van het locatietype van **Regio** naar [**Integratieserviceomgeving**](connect-virtual-network-vnet-isolated-environment-overview.md) is van invloed op het [prijsmodel](logic-apps-pricing.md#fixed-pricing) van uw logische app dat wordt gebruikt voor facturering, [limieten,](logic-apps-limits-and-config.md#integration-account-limits) [integratieaccountondersteuning,](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)enzovoort. Voordat u een ander locatietype selecteert, moet u de resulterende impact op uw logische app begrijpen.

1. Open in Visual Studio het azure resourcegroep-project dat uw logische app bevat.

1. Open in Solution `<logic-app-name>.json` Explorer het snelmenu van het bestand en selecteer **Openen met Logic App Designer**. (Toetsenbord: Ctrl + L)

   ![Het Json-bestand van de logische app openen met Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Als u deze opdracht niet hebt in Visual Studio 2019, controleert u of u de nieuwste updates voor Visual Studio en de extensie Azure Logic Apps Tools hebt.

1. Zorg ervoor dat de Logic App Designer zich focust door het tabblad of het oppervlak van de ontwerper te selecteren, zodat in het venster Eigenschappen de eigenschappen **Locatietype** en **Locatie** kiezen voor uw logische app worden weergegeven. Het locatietype van het project is ingesteld op **regio-** of **integratieserviceomgeving.**

   ![Eigenschappenvenster - 'Locatietype kiezen' & eigenschappen 'Locatie'.](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Als het venster Eigenschappen nog niet is geopend, selecteert u in het menu **Weergave** de optie **Venster Eigenschappen**. (Toetsenbord: Druk op F4)

1. Als u het locatietype wilt wijzigen, opent u de eigenschappenlijst **Locatietype kiezen** en selecteert u het gewenste locatietype.

   Als het locatietype bijvoorbeeld **integratieserviceomgeving**is, u **Regio**selecteren.

   ![De eigenschap Locatietype kiezen - locatietype wijzigen](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Als u de specifieke locatie wilt wijzigen, opent u de eigenschappenlijst **Locatie.** Selecteer op basis van het locatietype de gewenste locatie, bijvoorbeeld:

   * Selecteer een ander Azure-gebied:

     ![De eigenschappenlijst 'Locatie' openen, een andere Azure-regio selecteren](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Selecteer een andere ISE:

     ![Open de eigenschappenlijst 'Locatie', selecteer een andere ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Als u klaar bent, moet u uw Visual Studio-oplossing opslaan.

Wanneer u het locatietype of de locatie in Visual Studio wijzigt en uw logische app opslaat als een Azure Resource Manager-sjabloon, bevat die sjabloon ook parameterdeclaratie voor dat locatietype en locatie. Zie [Overzicht: Implementatie van logische apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)voor meer informatie over sjabloonparameters en logische apps.

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Vernieuwen vanuit Azure

Als u uw logische app bewerkt in de Azure-portal en deze wijzigingen wilt behouden, moet u ervoor zorgen dat u de versie van die app in Visual Studio vernieuwt met deze wijzigingen.

* Selecteer In Visual Studio op de werkbalk Logic App Designer de optie **Vernieuwen**.

  -of-

* Open in Visual Studio Cloud Explorer het snelmenu van de logische app en selecteer **Vernieuwen**.

![Logica-app vernieuwen met updates](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Updates van de logische app publiceren

Wanneer u klaar bent om uw logische app-updates van Visual Studio naar Azure te implementeren, selecteert u **publiceren**op de werkbalk Logic App Designer .

![Bijgewerkte logische app publiceren naar Azure-portal](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Logische app handmatig uitvoeren

U handmatig een logische app activeren die in Azure is geïmplementeerd vanuit Visual Studio. Selecteer Op de werkbalk Logic App Designer de optie **Trigger uitvoeren**.

![Handmatig trigger uitvoeren voor uw logische app](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Uitvoeringsgeschiedenis controleren

Als u de status wilt controleren en problemen met het uitvoeren van logische apps wilt diagnosticeren, u de details, zoals ingangen en uitvoer, bekijken voor de uitvoeringen in Visual Studio.

1. Open in Cloud Explorer het snelmenu van uw logische app en selecteer **Run-geschiedenis openen**.

   ![Run-geschiedenis openen voor uw logica-app](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Als u de details voor een specifieke run wilt weergeven, dubbelklikt u op een run. Bijvoorbeeld:

   ![Informatie over specifieke run weergeven](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Als u de tabel wilt sorteren op eigenschap, selecteert u de kolomkop voor die eigenschap.

1. Vouw de stappen uit waarvan u de invoer en uitvoer wilt bekijken, bijvoorbeeld:

   ![Ingangen en uitvoer voor elke stap weergeven](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Logische app in- of uitschakelen

Zonder uw logische app te verwijderen, u voorkomen dat de trigger de volgende keer wordt geactiveerd wanneer aan de triggervoorwaarde is voldaan. Als u uw logica-app uitschakelt, voorkomt u dat het logic-apps-engine toekomstige werkstroomexemplaren voor uw logische app maakt en uitvoert. Open in Cloud Explorer het snelmenu van de logische app en selecteer **Uitschakelen**.

![Uw logische app uitschakelen in Cloud Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Wanneer u een logische app uitschakelt, worden er geen nieuwe uitvoeringen geinstantieerd. Alle lopende en in behandeling zijnde runs worden voortgezet totdat ze zijn voltooid, wat enige tijd kan duren om te voltooien.

Als u uw logische app opnieuw wilt activeren, opent u in Cloud Explorer het snelmenu van uw logische app en selecteert u **Inschakelen**.

![Logische app inschakelen in Cloud Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Uw logische app verwijderen

Als u uw logische app wilt verwijderen uit de Azure-portal, opent u in Cloud Explorer het snelmenu van uw logische app en selecteert u **Verwijderen**.

![Uw logische app verwijderen uit Azure-portal](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Wanneer u een logische app verwijdert, worden geen nieuwe uitvoeringen gemaakt. Alle uitvoeringen die bezig zijn en wachten op uitvoering worden geannuleerd. Als u duizenden uitvoeringen hebt, kan de annulering een aanzienlijke tijd in beslag nemen. 

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer u uw logische app-project opent in de Logic Apps Designer, krijgt u mogelijk niet de optie voor het selecteren van uw Azure-abonnement. In plaats daarvan wordt uw logische app geopend met een Azure-abonnement dat niet het abonnement is dat u wilt gebruiken. Dit gedrag treedt op omdat Visual Studio het eerste geselecteerde abonnement voor toekomstig gebruik in de cache opslaat nadat u het JSon-bestand van een logische app hebt geopend. Als u dit probleem wilt oplossen, probeert u een van de volgende stappen:

* Wijzig de naam van het Json-bestand van de logische app. De abonnementscache is afhankelijk van de bestandsnaam.

* Als u eerder geselecteerde abonnementen voor *alle* logische apps in uw oplossing wilt verwijderen, verwijdert u de verborgen map met Instellingen van Visual Studio (.vs) in de map van uw oplossing. Op deze locatie worden uw abonnementsgegevens opgeslagen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u geïmplementeerde logische apps beheert met Visual Studio. Lees vervolgens meer over het aanpassen van logische app-definities voor implementatie:

> [!div class="nextstepaction"]
> [Definities van logische apps voor auteurs in JSON](../logic-apps/logic-apps-author-definitions.md)
