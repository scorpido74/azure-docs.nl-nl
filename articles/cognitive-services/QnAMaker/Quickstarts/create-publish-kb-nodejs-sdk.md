---
title: 'Quickstart: QnA Maker-client bibliotheek voor node. js'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de QnA Maker-client bibliotheek voor node. js. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: ab3fa02f7285fdbde918f9e365d1ee96ba5e71a1
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802996"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>Quickstart: QnA Maker-client bibliotheek voor node. js

Ga aan de slag met de QnA Maker-client bibliotheek voor node. js. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen. 

Gebruik de QnA Maker-client bibliotheek voor node. js voor het volgende:

* Een kennisdatabase maken 
* Een kennis database beheren
* Een kennisdatabase publiceren

[Naslag informatie over](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics) | het[bron code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | pakket voor de documentatie bibliotheek[(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [. js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) -voor beelden

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [node. js](https://nodejs.org).

## <a name="setting-up"></a>Instellen

### <a name="create-a-qna-maker-azure-resource"></a>Een QnA Maker Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor QnA Maker met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. 

Nadat u een sleutel van uw resource hebt opgehaald, [maakt u omgevings variabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor `QNAMAKER_SUBSCRIPTION_KEY` de `QNAMAKER_HOST`resource met de naam en. Gebruik de sleutel-en eindpunt waarden die zijn gevonden op de **sleutels** en **overzichts** pagina's van de resource in de Azure Portal.

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer de `npm init -y` opdracht uit om een knooppunt toepassing met een `package.json` bestand te maken. 

```console
npm init -y
```

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer de `ms-rest-azure` en `azure-cognitiveservices-qnamaker` NPM-pakketten:

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

Het bestand van `package.json` uw app wordt bijgewerkt met de afhankelijkheden.


## <a name="object-model"></a>Object model

De QnA Maker-client is een [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) -object dat wordt geverifieerd bij Azure met behulp van ServiceClientCredentials, dat uw sleutel bevat.

Nadat de client is gemaakt, gebruikt u de [Knowledge Base](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) -eigenschap uw kennis database maken, beheren en publiceren. 

Uw Knowledge Base beheren door een JSON-object te verzenden. Voor directe bewerkingen retourneert een methode meestal een JSON-object dat de status aangeeft. Voor langlopende bewerkingen is het antwoord de bewerkings-ID. Roep de [client aan. Methode Operations. getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) met de bewerkings-id om de [status van de aanvraag](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest)te bepalen. 

 
## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de QnA Maker-client bibliotheek voor node. js:

* [Een kennisdatabase maken](#create-a-knowledge-base)
* [Een kennisdatabase bijwerken](#update-a-knowledge-base)
* [Een kennisdatabase publiceren](#publish-a-knowledge-base)
* [Een Knowledge Base verwijderen](#delete-a-knowledge-base)
* [Status van een bewerking ophalen](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Maak een bestand met de naam `index.js`. Voeg de QnA Maker-bibliotheek en de Azure REST-bibliotheek toe aan het bestand.

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

Maak variabelen voor het Azure-eind punt en de sleutel van uw resource. Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

|Omgevingsvariabele|Node. js-variabele|Voorbeeld|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|32 teken-GUID|
|`QNAMAKER_HOST`|`endpoint`|`https://your-resource-name.api.cognitive.microsoft.com`: Vervang het subdomein `your-resource-name` door de waarde van de naam van uw resource|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>De client verifiëren

Maak vervolgens een ServiceClientCredentials-object met uw sleutel en gebruik dit met uw eind punt om een [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) -object te maken. Gebruik het client object om een [Knowledge Base](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) -object op te halen.


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

In een Knowledge Base worden vraag-en antwoord paren voor het [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) -object uit drie bronnen opgeslagen:

* Gebruik het [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) -object voor **redactionele inhoud**.
* Voor **bestanden**gebruikt u het [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) -object. 
* Gebruik voor **url's**een lijst met teken reeksen.

Roep de [Create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) -methode aan en geef de geretourneerde bewerkings-id door aan de methode [Operations. getDetails](#get-status-of-an-operation) om de status te controleren. 

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]

Zorg ervoor dat u de [`wait_for_operation`](#get-status-of-an-operation) functie include gebruikt, waarnaar wordt verwezen in de bovenstaande code om een Knowledge Base te kunnen maken. 

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U kunt een Knowledge Base bijwerken door door te geven in de Knowledge Base-ID en een [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) met DTO-objecten [toevoegen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [bijwerken](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)en [verwijderen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) voor de [Update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) methode. Gebruik de methode [Operation. getDetail](#get-status-of-an-operation) om te bepalen of de update is geslaagd.

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

Zorg ervoor dat u de [`wait_for_operation`](#get-status-of-an-operation) functie include gebruikt, waarnaar wordt verwezen in de bovenstaande code om een Knowledge Base te kunnen bijwerken. 

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de Knowledge Base met behulp van de methode [Publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) . Dit maakt het huidige opgeslagen en getrainde model, waarnaar wordt verwezen door de Knowledge Base-ID en publiceert die op een eind punt. 

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de Knowledge Base met behulp van de methode [Delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) met een para meter van de Knowledge Base-id. 

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>Status van een bewerking ophalen

Sommige methoden, zoals maken en bijwerken, kunnen voldoende tijd in beslag nemen, in plaats van te wachten tot het proces is voltooid, een [bewerking](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) wordt geretourneerd. Gebruik de [bewerkings-id](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) van de bewerking om te pollen (met pogings logica) om de status van de oorspronkelijke methode te bepalen. 

De _setTimeout_ -aanroep in het volgende code blok wordt gebruikt voor het simuleren van asynchrone code. Vervang dit door de logica voor opnieuw proberen. 

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit `node index.js` met de opdracht uit de toepassingsmap.


Alle code fragmenten in dit artikel zijn [beschikbaar](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) en kunnen als één bestand worden uitgevoerd.

```console
node index.js
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Zelfstudie: Een KB maken en beantwoorden](../tutorials/create-publish-query-in-portal.md)

* [Wat is de QnA Maker-API?](../Overview/overview.md)
* [Een kennisdatabase bewerken](../how-to/edit-knowledge-base.md)
* [Gebruiks analyse ophalen](../how-to/get-analytics-knowledge-base.md)