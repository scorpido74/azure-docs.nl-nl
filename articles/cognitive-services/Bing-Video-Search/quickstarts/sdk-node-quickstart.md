---
title: "Snelstartgids: zoeken naar Video's met behulp van de SDK voor node. js-Bing Video Search"
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om zoekaanvragen voor video's te verzenden met de Bing Video's zoeken-SDK voor Node.js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: aahi
ms.openlocfilehash: 5c8bd4ccadcc3c1947905e6bd74b48045a62ab57
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383744"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>Quick Start: een video zoekopdracht uitvoeren met de Bing Video Search SDK voor node. js

Gebruik deze quickstart om nieuws te zoeken met de Bing Video's zoeken-SDK voor Node.js. Hoewel Bing Video's zoeken een REST API heeft die compatibel is met de meeste programmeertalen, biedt de SDK een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Deze bevat meer aantekeningen en functies.

## <a name="prerequisites"></a>Vereisten

- [Node.js](https://www.nodejs.org/)

Stel een consoletoepassing in met de Bing Video Search SDK:
* Voer `npm install ms-rest-azure` uit in uw ontwikkelomgeving.
* Voer `npm install azure-cognitiveservices-videosearch` uit in uw ontwikkelomgeving.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor en voeg een instructie `require()` toe voor de Bing Video's zoeken-SDK en `CognitiveServicesCredentials`-module. Maak een variabele voor uw abonnementssleutel. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Maak een exemplaar van `CognitiveServicesCredentials` met uw sleutel. Gebruik deze vervolgens om een exemplaar te maken van de client voor het zoeken van video's.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>De zoekaanvraag verzenden

1. Gebruik `client.videosOperations.search()` om een zoekopdracht te verzenden naar de Bing Video's zoeken-API. Wanneer de lijst met zoekresultaten wordt geretourneerd, gebruikt u `.then()` om het resultaat op te slaan.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zie ook 

* [Wat is de Bing Video's zoeken-API?](../overview.md)
* [Voorbeelden voor Cognitive Services .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)