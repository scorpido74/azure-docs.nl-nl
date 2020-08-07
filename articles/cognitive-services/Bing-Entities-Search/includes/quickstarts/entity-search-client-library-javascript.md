---
title: Quickstart voor Bing Entity Search-clientbibliotheek voor JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: f5a3b48fd6be48d468b66009a77de100962be10d
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405060"
---
Gebruik deze quickstart om te zoeken naar entiteiten met de Bing Entity Search-clientbibliotheek voor JavaScript. Hoewel Bing Entity Search een REST API heeft die compatibel is met de meeste programmeertalen, biedt de clientbibliotheek een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Vereisten

* Nieuwste versie van [Node.js](https://nodejs.org/en/download/).

* De [Bing Entiteiten zoeken-SDK voor Node.js](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

De Bing Entiteiten zoeken-SDK installeren:

1. Voer `npm install ms-rest-azure` uit in uw ontwikkelomgeving.
2. Voer `npm install @azure/cognitiveservices-entitysearch` uit in uw ontwikkelomgeving.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor en voeg de volgende vereisten toe.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. Maak een exemplaar van `CognitiveServicesCredentials` met behulp van uw abonnementssleutel. Maak er vervolgens een exemplaar van de zoekclient mee.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Een aanvraag verzenden en een antwoord ontvangen

1. Verzend aanvraag voor het zoeken van entiteiten met `entitiesOperations.search()`. Geef nadat een antwoord is ontvangen de `queryContext` weer, het aantal geretourneerde resultaten en de beschrijving van het eerste resultaat.

    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../../tutorial-bing-entities-search-single-page-app.md)

* [Wat is de Bing Entiteiten zoeken-API?](../../overview.md)