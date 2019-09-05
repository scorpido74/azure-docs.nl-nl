---
title: Geautomatiseerde werk stromen maken en beheren met Visual Studio code-Azure Logic Apps
description: Quick start voor het maken en beheren van logische apps met JSON in Visual Studio code (VS code)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, deli, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/05/2018
ms.openlocfilehash: b9f9a402ecde09dd00c2b070f784858a141e556b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309007"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Quickstart: Geautomatiseerde werk stromen voor logische apps maken en beheren-Visual Studio code

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en Visual Studio code kunt u logische apps maken en beheren die u helpen bij het automatiseren van taken, werk stromen en processen voor het integreren van apps, gegevens, systemen en services in organisaties en ondernemingen. In deze Quick start ziet u hoe u werk stroom definities voor logische apps kunt maken en bewerken door te werken met het werk stroom definitie schema in JavaScript Object Notation (JSON) via een op code gebaseerde ervaring. U kunt ook werken met bestaande Logic apps die al zijn geïmplementeerd in Azure. 

Hoewel u deze taken kunt uitvoeren in de Azure Portal (https://portal.azure.com) en in Visual Studio kunt u sneller aan de slag met Visual Studio code wanneer u al bekend bent met definities van logische apps en rechtstreeks in code wilt werken. U kunt bijvoorbeeld al gemaakte Logic apps uitschakelen, inschakelen, verwijderen en vernieuwen. Daarnaast kunt u werken met Logic apps en integratie accounts vanuit elk ontwikkel platform waarop Visual Studio code wordt uitgevoerd, zoals Linux, Windows en Mac.

Voor dit artikel kunt u dezelfde logische app maken als in de Quick Start [voor het maken van een logische app in de Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), die gericht is op de basis concepten. In Visual Studio code ziet de logische app eruit als in dit voor beeld:

![Voltooide logische app](./media/create-logic-apps-visual-studio-code/overview.png)

Controleer voordat u begint of u deze items hebt:

* Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basis kennis over [werk stroom definities voor logische apps](../logic-apps/logic-apps-workflow-definition-language.md) en hun structuur, die gebruikmaakt van JavaScript object NOTATION (JSON) 

  Als u nog geen ervaring hebt met Logic Apps, kunt u de Snelstartgids proberen [om uw eerste logische app te maken in de Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), die gericht is op de basis concepten. 

* Toegang tot internet om u aan te melden bij Azure en uw Azure-abonnement

* Download en installeer deze hulpprogramma's als u ze nog niet hebt: 

  * [Visual Studio code versie 1.25.1 of hoger](https://code.visualstudio.com/), wat gratis is

  * Visual Studio code Extension voor Azure Logic Apps

    U kunt deze uitbrei ding downloaden en installeren vanuit Visual [Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) of rechtstreeks vanuit Visual Studio code. 
    Zorg ervoor dat u Visual Studio code opnieuw laadt na de installatie. 

    ![Zoek "Visual Studio code extension for Azure Logic Apps"](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Als u wilt controleren of de uitbrei ding correct is geïnstalleerd, wordt het pictogram van Azure weer gegeven in de werk balk van de Visual Studio-code. 

    ![Uitbrei ding geïnstalleerd](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Zie [uitbreidings Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery)voor meer informatie. U kunt ook bijdragen aan de open-source-versie van deze uitbrei ding bekijken en verzenden door naar de [Azure Logic apps-extensie voor Visual Studio code op github](https://github.com/Microsoft/vscode-azurelogicapps)te gaan. 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

1. Open Visual Studio Code. Selecteer op de werk balk van Visual Studio code het pictogram van Azure. 

   ![Azure-pictogram selecteren](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. Selecteer in het venster Azure onder **Logic apps**de optie **Aanmelden bij Azure**. 

   ![Selecteer aanmelden bij Azure](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   U wordt nu gevraagd om u aan te melden met behulp van de meegeleverde verificatie code. 

1. Kopieer de verificatie code en kies vervolgens **kopiëren & openen**, waarmee een nieuw browser venster wordt geopend.

   ![Aanmeldings prompt](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Voer uw verificatie code in. Als u hierom wordt gevraagd, kiest u **door gaan**.

   ![Code invoeren](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Selecteer uw Azure-account. Nadat u zich hebt aangemeld, kunt u uw browser sluiten en terugkeren naar Visual Studio code.

   In het venster van Azure worden in het deel venster Logic Apps en integratie accounts nu de Azure-abonnementen weer gegeven in uw account. 

   ![Abonnement selecteren](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Als u de verwachte abonnementen niet ziet, klikt u naast **Logic apps** label op **abonnementen selecteren** (filter pictogram). Zoek en selecteer de gewenste abonnementen.

1. Als u bestaande Logic apps of integratie accounts in uw Azure-abonnement wilt weer geven, vouwt u uw abonnement uit.

   ![Logic apps en integratie accounts weer geven](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Logische app maken

1. Als u zich niet hebt aangemeld bij uw Azure-abonnement vanuit Visual Studio code, volgt u de stappen in dit artikel om u nu aan te [melden](#sign-in-azure).

1. Selecteer **maken**in het context menu van uw abonnement.

   ![Selecteer maken](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. Selecteer een bestaande resource groep in de lijst met Azure-resource groepen in uw abonnement of **Maak een nieuwe resource groep**. 

   In dit voor beeld wordt een nieuwe resource groep gemaakt:

   ![Nieuwe resourcegroep maken](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Geef een naam op voor de Azure-resource groep en druk op ENTER.

   ![Geef een naam op voor de resource groep](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Selecteer de locatie van het Data Center waar de meta gegevens van uw logische app moeten worden opgeslagen.

   ![Locatie selecteren](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Geef een naam op voor uw logische app en druk vervolgens op ENTER.

   ![De logische app een naam](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Uw nieuwe logische app wordt nu weer gegeven in het Azure-venster onder uw Azure-abonnement. U kunt nu beginnen met het maken van de werk stroom definitie van de logische app.

1. Selecteer in het snelmenu van de logische app de optie **openen in editor**. 

   ![Logische app openen in editor](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   Met Visual Studio code wordt een sjabloon voor de werk stroom van een logische App geopend (. logicapp. JSON-bestand), zodat u de werk stroom van de logische app kunt gaan maken.

   ![Nieuwe definitie van werk stroom voor logische apps](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. Begin met het maken van de werk stroom definitie van de logische app in het sjabloon bestand van de werk stroom voor logische apps. Zie voor technische Naslag informatie het [schema voor de werk stroom definitie taal voor Azure Logic apps](../logic-apps/logic-apps-workflow-definition-language.md).

   Hier volgt een voor beeld van een logische definitie. Normaal gesp roken worden JSON-elementen in elke sectie alfabetisch weer gegeven, maar in dit voor beeld worden deze elementen ongeveer weer gegeven in de volg orde waarin de stappen van de logische app in de ontwerp functie worden weer gegeven.

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. Wanneer u klaar bent, slaat u het definitie bestand van de logische app op. Wanneer u met Visual Studio code wordt gevraagd om te bevestigen dat u de definitie van de logische app naar uw Azure-abonnement uploadt, kiest u **uploaden**.

   ![Uw nieuwe logische app uploaden](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Nadat Visual Studio code uw logische app naar Azure publiceert, kunt u uw app nu live vinden en uitvoeren in de Azure Portal. 

   ![De logische app is gepubliceerd in Azure Portal](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Logische app bewerken

Als u wilt werken met een bestaande logische app die al in Azure is geïmplementeerd, kunt u het werk stroom definitie bestand van de app openen in Visual Studio code.

1. Als u zich niet hebt aangemeld bij uw Azure-abonnement vanuit Visual Studio code, volgt u de stappen in dit artikel om u nu aan te [melden](#sign-in-azure).

1. In het venster Azure, onder **Logic apps**, vouwt u uw Azure-abonnement uit en selecteert u de logische app die u wilt. 

1. Selecteer in het menu van de logische app de optie **openen in editor**. Of Kies naast de naam van de logische app het bewerkings pictogram.

   ![Editor openen voor een bestaande logische app](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio code opent het bestand. logicapp. json voor de werk stroom definitie van de logische app.

   ![Werk stroom definitie van de logische App geopend](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Breng uw wijzigingen aan in de definitie van uw logische app.

1. Sla de wijzigingen op als u klaar bent.

1. Wanneer Visual Studio code u vraagt om de definitie van de logische app bij te werken in uw Azure-abonnement, kiest u **uploaden**. 

   ![Uw bewerkingen uploaden](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> * [Logische apps maken met Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)