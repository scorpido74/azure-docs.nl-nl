---
title: Quickstart voor Bing News Search-clientbibliotheek voor JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: cc96233ea6e2d02f3c3a2036466e3934aa234f5b
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407967"
---
Gebruik deze quickstart om aan de slag te gaan met de Bing News Search-clientbibliotheek voor Java om nieuws te zoeken. Hoewel Bing News Search een REST API heeft die compatibel is met de meeste programmeertalen, biedt de clientbibliotheek een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Vereisten

* [Node.js](https://nodejs.org/en/)

Stel een consoletoepassing in met de Bing News Search-clientbibliotheek:
1. Voer `npm install ms-rest-azure` uit in uw ontwikkelomgeving.
2. Voer `npm install azure-cognitiveservices-newssearch` uit in uw ontwikkelomgeving.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een instantie van de `CognitiveServicesCredentials`. Maak variabelen voor uw abonnementssleutel en zoekterm.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. Maak een instantie van de client:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Een zoekquery verzenden

1. Gebruik de client om te zoeken met een queryterm, in dit geval 'Winter Olympics':
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

De code geeft `result.value` items weer in de console zonder tekst te parseren. De resultaten, indien van toepassing per categorie, omvatten:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../../tutorial-bing-news-search-single-page-app.md)
