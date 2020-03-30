---
title: Eerste serverloze app bouwen in Visual Studio
description: Een serverloze app bouwen, implementeren en beheren met Azure Logic Apps en Azure-functies in Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981154"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Uw eerste serverloze app bouwen met Azure Logic Apps en Azure-functies in Visual Studio

U snel cloud-apps ontwikkelen en implementeren met behulp van de hulpprogramma's en mogelijkheden zonder server in Azure, zoals [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en Azure [Functions.](../azure-functions/functions-overview.md) In dit artikel ziet u hoe u beginnen met het bouwen van een serverloze app, die gebruikmaakt van een logische app die een Azure-functie aanroept, in Visual Studio. Zie [Azure Serverless met functies en logische apps](../logic-apps/logic-apps-serverless-overview.md)voor meer informatie over serverloze oplossingen in Azure.

## <a name="prerequisites"></a>Vereisten

Als u een serverloze app wilt bouwen in Visual Studio, hebt u het:

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De volgende tools. Als je ze nog niet hebt, download en installeer ze dan.

  * [Visual Studio 2019, 2017 of 2015 (Community of andere editie)](https://aka.ms/download-visual-studio). 
  Deze snelstart maakt gebruik van Visual Studio Community 2017, dit is gratis.

    > [!IMPORTANT]
    > Wanneer u Visual Studio 2019 of 2017 installeert, moet u de **Azure-ontwikkelingswerkbelasting** selecteren.

  * [Microsoft Azure SDK voor .NET (versie 2.9.1 of hoger)](https://azure.microsoft.com/downloads/). 
  Meer informatie over [Azure SDK voor .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Hulpmiddelen voor Azure Logic Apps voor de gewenste Visual Studio-versie:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    U kunt hulpprogramma's van Azure Logic Apps ofwel rechtstreeks vanuit Visual Studio Marketplace downloaden en installeren, of leer [deze extensie te installeren vanuit Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Zorg ervoor dat u Visual Studio opnieuw opstart na de installatie.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) voor lokaal foutopsporingsfuncties.

* Toegang tot het web tijdens het gebruik van de ingesloten Logic App Designer.

  De ontwerpfunctie moet over een internetverbinding beschikken om resources te maken in Azure en de eigenschappen en gegevens van connectoren in uw logische app te lezen. 
  Als u bijvoorbeeld de Dynamics CRM Online-connector gebruikt, controleert de ontwerpfunctie uw CRM-exemplaar voor de beschikbare standaardregels en aangepaste eigenschappen.

## <a name="create-a-resource-group-project"></a>Een resourcegroepproject maken

Maak om aan de slag te gaan een [Azure Resource Group-project](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) voor uw serverloze app. In Azure maakt u resources binnen een *resourcegroep*, een logische verzameling die u gebruikt voor het organiseren, beheren en implementeren van resources voor een hele app als één asset. Voor een serverloze app in Azure bevat uw brongroep resources voor zowel Azure Logic Apps als Azure Functions. Meer informatie over [Azure-resourcegroepen en resources](../azure-resource-manager/management/overview.md).

1. Start Visual Studio en meld u aan met uw Azure-account.

1. In het menu **File** selecteert u **New** > **Project**.

   ![Nieuw project maken in Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Onder **Installed**, selecteert u **Visual C#** of **Visual Basic**. Selecteer vervolgens **Cloud** > **Azure Resource Group**.

   > [!NOTE]
   > Als de **categorie Cloud** of Azure **Resource Group** niet bestaat, controleert u of u de Azure SDK voor Visual Studio hebt geïnstalleerd.

   Als u Visual Studio 2019 gebruikt, voert u de volgende stappen uit:

   1. Selecteer **in het** vak Een nieuw project maken de projectsjabloon Azure **Resource Group** voor Visual C# of Visual Basic en selecteer **Volgende**.

   1. Geef de naam en andere projectgegevens op die u wilt gebruiken voor de Azure-brongroep. Als u gereed bent, selecteert u **Maken**.

1. Geef uw project een naam en een locatie en selecteer **OK**.

   Visual Studio vraagt u een sjabloon te selecteren in de lijst met sjablonen. 
   In dit voorbeeld wordt een Azure QuickStart-sjabloon gebruikt, zodat u een serverloze app maken met een logische app en een aanroep naar een Azure-functie.

   > [!TIP]
   > In scenario's waarin u uw oplossing niet vooraf wilt implementeren in een Azure-brongroep, u de lege **Logic App-sjabloon** gebruiken, die alleen een lege logische app maakt.

1. Selecteer **Azure QuickStart (github.com/Azure/azure-quickstart-templates)** in de lijst **Sjablonen weergeven in deze lijst** met locaties.

1. Voer in het zoekvak 'logica-app' in als filter. Selecteer in de resultaten de sjabloon **101-logic-app-and-function-app.**

   ![Azure QuickStart-sjabloon selecteren](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio maakt en opent een oplossing voor uw resourcegroepproject. 
   De Azure QuickStart-sjabloon die u hebt geselecteerd, maakt een implementatiesjabloon met de naam azuredeploy.json in uw resourcegroepproject. Deze implementatiesjabloon bevat de definitie voor een eenvoudige logische app die wordt geactiveerd door een HTTP-aanvraag, een Azure-functie aanroept en het resultaat retourneert als een HTTP-antwoord.

   ![Nieuwe serverloze oplossing](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Implementeer vervolgens uw oplossing in Azure. U moet dit doen voordat u de implementatiesjabloon openen en de bronnen voor uw serverloze app bekijken.

## <a name="deploy-your-solution"></a>Uw oplossing implementeren

Voordat u uw logische app openen in de Logic App Designer in Visual Studio, moet u een Azure-brongroep hebben die al is geïmplementeerd in Azure. De ontwerper kan vervolgens verbindingen maken met resources en services in uw logische app. Voer voor deze taak de volgende stappen uit om uw oplossing vanuit Visual Studio te implementeren in de Azure-portal:

1. Selecteer in Solution Explorer in het snelmenu van uw resourceproject de optie**Nieuw** **implementeren** > .

   ![Nieuwe implementatie voor resourcegroep maken](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Als ze nog niet zijn geselecteerd, selecteert u uw Azure-abonnement en de brongroep waaraan u wilt implementeren. Selecteer vervolgens **Implementeren**.

   ![Implementatie-instellingen](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Als het vak **Parameters bewerken** wordt weergegeven, geeft u de resourcenamen op die u wilt gebruiken voor uw logische app en uw Azure-functie-app bij implementatie en slaat u de instellingen op. Zorg ervoor dat u een wereldwijd unieke naam gebruikt voor uw functie-app.

   ![Namen voor uw logische app en functie-app geven](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Wanneer Visual Studio de implementatie naar uw opgegeven resourcegroep start, wordt de implementatiestatus van uw oplossing weergegeven in het venster **Weergave van** de Visual Studio. 
   Nadat de implementatie is voltooid, is uw logische app live in de Azure-portal.

## <a name="edit-your-logic-app-in-visual-studio"></a>Uw logica-app bewerken in Visual Studio

Als u uw logische app na implementatie wilt bewerken, opent u uw logische app met behulp van de Logic App Designer in Visual Studio.

1. Selecteer in Solution Explorer in het snelmenu van het azuredeploy.json-bestand de optie **Openen met Logic App Designer**.

   ![Azuredeploy.json openen in Logic App Designer](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Als je deze opdracht niet hebt in Visual Studio 2019, controleer dan of je de nieuwste updates voor Visual Studio hebt.

1. Nadat het vak **Eigenschappen van Logic App** is weergegeven, selecteert u onder **Abonnement**uw Azure-abonnement als het nog niet is geselecteerd. Selecteer **onder Resourcegroep**de resourcegroep en -locatie waar u uw oplossing hebt geïmplementeerd en selecteer **OK**.

   ![Eigenschappen van logische app](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Nadat de Logic App Designer is geopend, u stappen blijven toevoegen of de werkstroom wijzigen en uw updates opslaan.

   ![Geopende logische app in Logic App Designer](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Uw Azure-functieproject maken

Als u uw functie voor functies wilt maken met JavaScript, Python, F#, PowerShell, Batch of Bash, voert u de stappen in [Het uitvoeren met Azure Functions Core Tools](../azure-functions/functions-run-local.md). Als u uw Azure-functie wilt ontwikkelen met C# in uw oplossing, gebruikt u een C#-klassenbibliotheek door de stappen te volgen in [Een .NET-klassenbibliotheek publiceren als functie-app](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Functies implementeren vanuit Visual Studio

Uw implementatiesjabloon implementeert alle Azure-functies die u in uw oplossing hebt, vanuit de Git-repo die is opgegeven door variabelen in het azuredeploy.json-bestand. Als u uw project Functions in uw oplossing maakt en auteurt, u dat project controleren in Git-bronbeheer (bijvoorbeeld GitHub of Azure DevOps) en de `repo` variabele bijwerken zodat de sjabloon uw Azure-functie implementeert.

## <a name="manage-logic-apps-and-view-run-history"></a>Logische apps beheren en rungeschiedenis weergeven

Voor logische apps die al in Azure zijn geïmplementeerd, u deze apps nog steeds bewerken, beheren, uitvoeren voor en uitschakelen vanuit Visual Studio.

1. Open **Cloud Explorer**in het menu **Weergave** in Visual Studio.

1. Selecteer **onder Alle abonnementen**het Azure-abonnement dat is gekoppeld aan de logische apps die u wilt beheren en selecteer Vervolgens **Toepassen**.

1. Selecteer **onder Logische apps**uw logische app. Selecteer **Openen met Logic App Editor**in het snelmenu van die app.

   > [!TIP]
   > Als je deze opdracht niet hebt in Visual Studio 2019, controleer dan of je de nieuwste updates voor Visual Studio hebt.

U nu de reeds gepubliceerde logica-app downloaden naar uw resourcegroepproject. Hoewel u dus een logische app hebt gestart in de Azure-portal, u die app nog steeds importeren en beheren in Visual Studio. Zie [Logische apps beheren met Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Logische apps beheren met Visual Studio](manage-logic-apps-with-visual-studio.md)
