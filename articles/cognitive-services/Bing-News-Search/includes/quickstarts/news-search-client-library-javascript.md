---
title: Snelstartgids voor Bing News Search java script-client bibliotheek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: 858e6b9e0e40ab988a4cdf04b31580c1ca28d40a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503893"
---
Gebruik deze Quick Start om te beginnen met zoeken naar nieuws met de Bing News Search-client bibliotheek voor Java script. Hoewel Bing News Search een REST API compatibel met de meeste programmeer talen heeft, biedt de client bibliotheek een eenvoudige manier om de service te integreren in uw toepassingen. De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Vereisten

* [Node.js](https://nodejs.org/en/)

Een console toepassing instellen met behulp van de Bing News Search-client bibliotheek:
1. Voer `npm install ms-rest-azure` uit in uw ontwikkelomgeving.
2. Voer `npm install azure-cognitiveservices-newssearch` uit in uw ontwikkelomgeving.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een instantie van de `CognitiveServicesCredentials`. Maak variabelen voor uw abonnements sleutel en een zoek term.

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
