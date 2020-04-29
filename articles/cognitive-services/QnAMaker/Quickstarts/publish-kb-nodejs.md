---
title: "Snelstartgids: QnA Maker met REST-Api's voor node. js"
description: In deze Quick start ziet u hoe u aan de slag gaat met de QnA Maker REST-Api's voor node. js. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: ecc3fb144fb4b4e27182567925199f841b1c4357
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851672"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Snelstartgids: QnA Maker REST-Api's voor node. js

Ga aan de slag met de QnA Maker REST-Api's voor node. js. Volg deze stappen om de voorbeeld code voor basis taken uit te proberen.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen.

Gebruik de QnA Maker REST-Api's voor node. js voor het volgende:

* Een kennisdatabase maken
* Een kennis database vervangen
* Een kennisdatabase publiceren
* Een knowledge base verwijderen
* Een Knowledge base downloaden
* Status van een bewerking ophalen

[Voor beelden van referentie documentatie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [node. js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [node. js](https://nodejs.org).
* U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Als u de sleutel en het eind punt (inclusief de resource naam) wilt ophalen, selecteert u **Quick** start voor uw resource in het Azure Portal.

## <a name="setting-up"></a>Instellen

### <a name="create-a-qna-maker-azure-resource"></a>Een QnA Maker Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor QnA Maker met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine.

Nadat u een sleutel van uw resource hebt opgehaald, [maakt u omgevings variabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor `QNAMAKER_RESOURCE_KEY` de `QNAMAKER_AUTHORING_ENDPOINT`resource met de naam en. Gebruik de sleutel-en eindpunt waarden die zijn gevonden op de **Quick** start-pagina van de resource in het Azure Portal.

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar.

```console
mkdir myapp && cd myapp
```

Voer de `npm init -y` opdracht uit om een knooppunt `package.json` bestand te maken.

```console
npm init -y
```

Voeg de `reqeuestretry` en `request` NPM-pakketten toe:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de QnA Maker REST-Api's voor node. js:

* [Een kennisdatabase maken](#create-a-knowledge-base)
* [Een kennis database vervangen](#replace-a-knowledge-base)
* [Een kennisdatabase publiceren](#publish-a-knowledge-base)
* [Een knowledge base verwijderen](#delete-a-knowledge-base)
* [Een Knowledge base downloaden](#download-the-knowledge-base)
* [Status van een bewerking ophalen](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Maak een bestand met `rest-apis.js` de naam en voeg _de volgende instructie_ toe om HTTP-aanvragen te maken.

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Informatie over Azure-resource toevoegen

Maak variabelen voor het Azure-eind punt en de sleutel van uw resource. Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

Stel de volgende omgevings waarden in:

* `QNAMAKER_RESOURCE_KEY`-De **sleutel** is een teken reeks van 32 en is beschikbaar in het Azure Portal op de QnA Maker-resource op de pagina **snel starten** . Dit is niet hetzelfde als de Voorspellings eindpunt sleutel.
* `QNAMAKER_AUTHORING_ENDPOINT`-Uw ontwerp-eind punt in de indeling van `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`bevat de naam van uw **resource**. Dit is niet dezelfde URL die wordt gebruikt om een query uit te zoeken op het Voorspellings eindpunt.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

In een Knowledge Base worden vraag-en antwoord paren opgeslagen, gemaakt op basis van een JSON-object van:

* **Redactionele inhoud**.
* **Bestanden** : lokale bestanden waarvoor geen machtigingen zijn vereist.
* **Url's** -openbaar beschik bare url's.

Gebruik de [rest API om een Knowledge Base te maken](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Een kennis database vervangen

Gebruik de [rest API om een Knowledge Base te vervangen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de Knowledge Base. Dit proces zorgt ervoor dat de Knowledge Base beschikbaar is via een HTTP-query Voorspellings eindpunt.

Gebruik de [rest API om een Knowledge Base te publiceren](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>De Knowledge base downloaden

Gebruik de [rest API om een Knowledge Base te downloaden](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Wanneer u klaar bent met de Knowledge Base, verwijdert u deze.

Gebruik de [rest API om een Knowledge Base te verwijderen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Status van een bewerking ophalen

Langlopende processen, zoals het aanmaak proces, retourneert een bewerkings-ID, die moet worden gecontroleerd met een afzonderlijke REST API aanroep. Deze functie neemt de hoofd tekst van het antwoord Create. De belang rijke sleutel is `operationState`de, waarmee wordt bepaald of u wilt door gaan met polling.

Gebruik de [rest API om bewerkingen op een Knowledge Base te bewaken](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit `node rest-apis.js` met de opdracht uit de toepassingsmap.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Zelf studie: een KB maken en beantwoorden](../tutorials/create-publish-query-in-portal.md)

* [Wat is de QnA Maker-API?](../Overview/overview.md)
* [Een knowledge base bewerken](../how-to/edit-knowledge-base.md)
* [Gebruiks analyse ophalen](../how-to/get-analytics-knowledge-base.md)
* De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).