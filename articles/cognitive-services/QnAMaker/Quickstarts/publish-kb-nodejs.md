---
title: "Quickstart: QnA Maker met REST API's voor Node.js"
description: Deze quickstart laat zien hoe je aan de slag met de QnA Maker REST API's voor Node.js. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: ecc3fb144fb4b4e27182567925199f841b1c4357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851672"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Quickstart: QnA Maker REST API's voor Node.js

Ga aan de slag met de QnA Maker REST API's voor Node.js. Volg deze stappen om de voorbeeldcode voor basistaken uit te proberen.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen.

Gebruik de QnA Maker REST API's voor Node.js om:

* Een kennisdatabase maken
* Een kennisbank vervangen
* Een kennisdatabase publiceren
* Een knowledge base verwijderen
* Download een kennisbank
* Status van een bewerking opdoen

[Referentiedocumentatie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Node.js-voorbeelden](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [Node.js](https://nodejs.org).
* U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Als u uw sleutel en eindpunt (inclusief de naam van de bron) wilt ophalen, selecteert u **Quickstart** voor uw resource in de Azure-portal.

## <a name="setting-up"></a>Instellen

### <a name="create-a-qna-maker-azure-resource"></a>Een Azure-bron van QnA Maker maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor QnA Maker met behulp van de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine.

Nadat u een sleutel uit uw resource hebt opgehaald, `QNAMAKER_AUTHORING_ENDPOINT`maakt u [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de benoemde `QNAMAKER_RESOURCE_KEY` resource en . Gebruik de sleutel- en eindpuntwaarden op de **quickstart-pagina** van de resource in de Azure-portal.

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor uw app en navigeer ernaar.

```console
mkdir myapp && cd myapp
```

Voer `npm init -y` de opdracht uit `package.json` om een knooppuntbestand te maken.

```console
npm init -y
```

Voeg `reqeuestretry` de `request` npm-pakketten en NPM toe:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de QnA Maker REST API's voor Node.js:

* [Een kennisdatabase maken](#create-a-knowledge-base)
* [Een kennisbank vervangen](#replace-a-knowledge-base)
* [Een kennisdatabase publiceren](#publish-a-knowledge-base)
* [Een knowledge base verwijderen](#delete-a-knowledge-base)
* [Download een kennisbank](#download-the-knowledge-base)
* [Status van een bewerking opdoen](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Maak een `rest-apis.js` bestand met de naam en voeg de volgende instructie _om_ HTTP-verzoeken uit te voeren.

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Azure-brongegevens toevoegen

Maak variabelen voor het Azure-eindpunt en de sleutel van uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

Stel de volgende omgevingswaarden in:

* `QNAMAKER_RESOURCE_KEY`- De **sleutel** is een tekenreeks met 32 tekens en is beschikbaar in de Azure-portal, op de QnA Maker-bron, op de pagina **Snel begin.** Dit is niet hetzelfde als de voorspellingeindpuntsleutel.
* `QNAMAKER_AUTHORING_ENDPOINT`- Uw ontwerpeindpunt, in de `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`vorm van , bevat uw **bronnaam**. Dit is niet dezelfde URL die wordt gebruikt om het voorspellingseindpunt op te vragen.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

Een kennisbank slaat vraag- en antwoordparen op, gemaakt op basis van een JSON-object van:

* **Redactionele inhoud**.
* **Bestanden** - lokale bestanden waarvoor geen machtigingen nodig zijn.
* **URL's** - openbaar beschikbare URL's.

Gebruik de [REST API om een kennisbank te maken.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Een kennisbank vervangen

Gebruik de [REST API om een kennisbank te vervangen.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de kennisbank. Dit proces maakt de kennisbank beschikbaar vanaf een eindpunt voor het voorspellen van HTTP-query's.

Gebruik de [REST API om een kennisbank te publiceren.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>Download de kennisbank

Gebruik de [REST API om een kennisbank te downloaden.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Wanneer u klaar bent met het gebruik van de kennisbank, verwijder deze dan.

Gebruik de [REST API om een kennisbank te verwijderen.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete)

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Status van een bewerking opdoen

Langlopende processen, zoals het creatieproces, retourneert een bewerkings-id, die moet worden gecontroleerd met een afzonderlijke REST API-aanroep. Deze functie neemt het lichaam van de reactie te creëren. De belangrijke sleutel `operationState`is de , die bepaalt of je nodig hebt om door te gaan met polling.

Gebruik de [REST API om bewerkingen op een kennisbank te controleren.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de `node rest-apis.js` toepassing met opdracht uit uw toepassingsmap uit.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Zelfstudie: Een KB maken en beantwoorden](../tutorials/create-publish-query-in-portal.md)

* [Wat is de QnA Maker API?](../Overview/overview.md)
* [Een knowledge base bewerken](../how-to/edit-knowledge-base.md)
* [Gebruiksanalyses bekijken](../how-to/get-analytics-knowledge-base.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js)