---
title: Quickstart voor de Bing Web Search-clientbibliotheek voor JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 20eaf6e6a9f0eee15e6ad6a5bd8f23bf8531545c
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376602"
---
Met de Bing Web Search-clientbibliotheek kunt u Bing Web Search eenvoudig integreren in uw Node.js-toepassing. In deze snelstartgids leert u hoe u een instantie maakt voor een client, een aanvraag verzendt en het antwoord weergeeft.

Wilt u de code nu zien? Voorbeelden voor de [Bing Search-clientbibliotheken voor JavaScript](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/tree/master/Samples) zijn beschikbaar op GitHub.

## <a name="prerequisites"></a>Vereisten
Voordat u verdergaat met deze snelstart moet u beschikken over:

* [Node.js 6](https://nodejs.org/en/download/) of later
* Een abonnementssleutel  

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]


## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

U moet eerst de ontwikkelomgeving voor het Node.js-project opzetten.

1. Maak een nieuwe map voor uw project:

    ```console
    mkdir YOUR_PROJECT
    ```

1. Maak een nieuw pakketbestand:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. Nu gaan we enkele Azure-modules installeren en toevoegen aan de `package.json`:

    ```console
    npm install --save @azure/cognitiveservices-websearch
    npm install --save @azure/ms-rest-azure-js
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Een project maken en de vereiste modules declareren

Maak in dezelfde map als uw `package.json` een nieuw Node.js-project met behulp van uw favoriete IDE of editor. Bijvoorbeeld: `sample.js`.

Vervolgens kopieert u deze code naar uw project. Hiermee worden de modules geladen die in de vorige sectie zijn geïnstalleerd.

```javascript
const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
const WebSearchAPIClient = require('@azure/cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Een instantie maken voor de client

Met deze code wordt een instantie gemaakt voor een client en wordt gebruikgemaakt van de module `@azure/cognitiveservices-websearch`. Zorg ervoor dat u een geldige abonnementssleutel voor uw Azure-account invoert voordat u verdergaat.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>Een aanvraag maken en de resultaten weergeven

Gebruik de client om een zoekquery te verzenden naar Bing Web Search. Als het antwoord resultaten bevat voor items in de `properties`-matrix, wordt de `result.value` weergegeven in de console.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>Het programma uitvoeren

Als laatste stap voert u het programma uit.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met dit project, moet u uw abonnementssleutel verwijderen uit de code van het programma.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Voorbeelden voor Cognitive Services Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>Zie ook

* [Naslaginformatie over Azure Node SDK](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-websearch/)
