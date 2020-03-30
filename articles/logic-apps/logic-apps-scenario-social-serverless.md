---
title: Dashboard voor klantinzichten maken
description: Gebruikersfeedback, sociale mediagegevens en meer beheren door een klantendashboard te bouwen met Azure Logic Apps en Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: e300bf9c9aa0acf0bed6426eb73f690f9a38bd74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980424"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Een dashboard voor streaming klantinzichten maken met Azure Logic Apps en Azure-functies

Azure biedt [serverloze](https://azure.microsoft.com/solutions/serverless/) tools waarmee u snel apps in de cloud bouwen en hosten, zonder na te hoeven denken over infrastructuur. In deze zelfstudie u een dashboard maken dat wordt geactiveerd op feedback van klanten, feedback analyseert met machine learning en inzichten publiceert naar een bron, zoals Power BI of Azure Data Lake.

Voor deze oplossing gebruikt u deze belangrijke Azure-componenten voor serverloze apps: [Azure-functies](https://azure.microsoft.com/services/functions/) en [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Azure Logic Apps biedt een serverloze workflow-engine in de cloud, zodat u orkestraties maken voor serverloze componenten en verbinding maken met meer dan 200 services en API's. Azure Functions biedt serverless computing in de cloud. Deze oplossing maakt gebruik van Azure-functies voor het markeren van klanttweets op basis van vooraf gedefinieerde zoekwoorden.

In dit scenario maakt u een logische app die wordt geactiveerd bij het vinden van feedback van klanten. Enkele connectors die u helpen te reageren op feedback van klanten zijn Outlook.com, Office 365, Survey Monkey, Twitter en een [HTTP-verzoek via een webformulier.](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/) De workflow die u maakt, bewaakt een hashtag op Twitter.

U [de volledige oplossing in Visual Studio bouwen](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) en de oplossing implementeren met azure resource [manager-sjabloon.](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md) Voor een video walkthrough die laat zien hoe deze oplossing te maken, [bekijk deze Channel 9 video](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Trigger op klantgegevens

1. Maak in de Azure-portal of Visual Studio een lege logische app. 

   Als u nieuw bent in logische apps, controleert u de [quickstart voor de Azure-portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) of de [quickstart voor Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. Zoek en voeg in Logic App Designer de Twitter-trigger met deze actie aan en voeg deze toe: **wanneer een nieuwe tweet wordt geplaatst**

3. Stel de trigger in om tweets te beluisteren op basis van een trefwoord of hashtag.

   Op polling-gebaseerde triggers, zoals de Twitter-trigger, bepaalt de eigenschap recidief hoe vaak de logische app controleert op nieuwe items.

   ![Voorbeeld van Twitter trigger][1]

Deze logica app wordt nu geactiveerd op alle nieuwe tweets. U vervolgens de tweetgegevens gebruiken en analyseren, zodat u de geuite gevoelens beter begrijpen. 

## <a name="analyze-tweet-text"></a>Tweettekst analyseren

Als u het sentiment achter bepaalde tekst wilt detecteren, u [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)gebruiken.

1. Kies in Logic App Designer onder de trigger de optie **Nieuwe stap**.

2. Zoek de **Text Analytics-connector.**

3. Selecteer de actie **Sentiment detecteren.**

4. Geef desgevraagd een geldige sleutel voor Cognitive Services voor de Text Analytics-service.

5. Selecteer **onder Aanvraagtekst**het veld **Tekst tweeten,** waarmee de tweettekst wordt weergegeven als invoer voor analyse.

Nadat je de tweetgegevens en inzichten over de tweet hebt gekregen, kun je nu verschillende andere relevante connectors en hun acties gebruiken:

* **Power BI - Rijen toevoegen aan streaminggegevensset:** bekijk binnenkomende tweets op een Power BI-dashboard.
* **Azure Data Lake - Toevoegend bestand:** Voeg klantgegevens toe aan een Azure Data Lake-gegevensset om op te nemen in analysetaken.
* **SQL - Rijen toevoegen:** gegevens opslaan in een database voor later ophalen.
* **Slack - Bericht verzenden:** Een Slack-kanaal op de hoogte stellen van negatieve feedback waarvoor mogelijk actie nodig is.

U ook een Azure-functie maken en een Azure-functie maken, zodat u aangepaste verwerkingen op uw gegevens uitvoeren. 

## <a name="process-data-with-azure-functions"></a>Gegevens verwerken met Azure-functies

Voordat u een functie maakt, maakt u een functie-app in uw Azure-abonnement. Als uw logische app rechtstreeks een functie aanroept, moet de functie bijvoorbeeld een HTTP-triggerbinding hebben, bijvoorbeeld de **httptriggersjabloon** gebruiken. Meer informatie over [het maken van uw eerste functie-app en -functie in de Azure-portal.](../azure-functions/functions-create-first-azure-function-azure-portal.md)

Gebruik voor dit scenario de tweettekst als de aanvraaginstantie voor uw Azure-functie. Definieer in uw functiecode de logica die bepaalt of de tweettekst een trefwoord of woordgroep bevat. Houd de functie zo eenvoudig of complex als nodig is voor het scenario.
Geef aan het einde van de functie een antwoord op de logische app met `containsKeyword` bepaalde gegevens, bijvoorbeeld een eenvoudige booleaanse waarde zoals of een complex object.

> [!TIP]
> Als u een complexe reactie van een functie in een logische app wilt openen, gebruikt u de actie **Parse JSON.**

Wanneer u klaar bent, slaat u de functie op en voegt u de functie als actie toe in de logische app die u bouwt.

## <a name="add-azure-function-to-logic-app"></a>Azure-functie toevoegen aan logische app

1. Kies in Logic App Designer onder de actie **Sentiment detecteren** de optie **Nieuwe stap**.

2. Zoek de **Azure Functions-connector** en selecteer vervolgens de functie die u hebt gemaakt.

3. Selecteer **Onder Aanvraaghoofd**de optie **Tekst tweeten**.

![De functiestap geconfigureerd][2]

## <a name="run-and-monitor-your-logic-app"></a>Uw logische app uitvoeren en bewaken

Als u huidige of eerdere uitvoeringen voor uw logische app wilt controleren, u de uitgebreide foutopsporings- en bewakingsmogelijkheden gebruiken die Azure Logic Apps biedt in de Azure-portal, Visual Studio of via de Azure REST-API's en SDK's.

Als u uw logische app eenvoudig wilt testen, kiest u in Logic App Designer **Trigger uitvoeren**. De triggerpolls voor tweets op basis van uw opgegeven schema totdat een tweet wordt gevonden die aan uw criteria voldoet. Terwijl de run vordert, toont de ontwerper een live weergave voor die run.

Ga als het gaat om eerdere run-geschiedenissen weer te geven in Visual Studio of de Azure-portal: 

* Open Visual Studio Cloud Explorer. Zoek uw logische app en open het snelmenu van de app. Selecteer **Run-geschiedenis openen**.

  > [!TIP]
  > Als je deze opdracht niet hebt in Visual Studio 2019, controleer dan of je de nieuwste updates voor Visual Studio hebt.

* Zoek uw logische app in de Azure-portal. Kies **Overzicht**in het menu van uw logische app. 

## <a name="create-automated-deployment-templates"></a>Sjablonen voor automatische implementatie maken

Nadat u een oplossing voor logische apps hebt gemaakt, u uw app vastleggen en implementeren als azure [resource manager-sjabloon](../azure-resource-manager/templates/overview.md) voor elke Azure-regio in de wereld. U deze mogelijkheid gebruiken om parameters te wijzigen voor het maken van verschillende versies van uw app en voor het integreren van uw oplossing in Azure Pipelines. U azure-functies ook opnemen in uw implementatiesjabloon, zodat u de volledige oplossing beheren met alle afhankelijkheden als één sjabloon. Meer informatie over het [automatiseren van het implementeren van logische apps](logic-apps-azure-resource-manager-templates-overview.md).

Schakel voor een voorbeeldimplementatiesjabloon met een Azure-functie de Azure [quickstart-sjabloonopslagplaats in](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Volgende stappen

* [Andere voorbeelden en scenario's voor Azure Logic Apps zoeken](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
