---
title: Snelstartgids voor Bing Video Search java script-client bibliotheek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: b642d981b79d13857881ec8cc5796e6365003ace
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289761"
---
Gebruik deze Quick Start om te beginnen met zoeken naar nieuws met de Bing Video Search-client bibliotheek voor Java script. Hoewel Bing Video Search een REST API compatibel met de meeste programmeer talen heeft, biedt de client bibliotheek een eenvoudige manier om de service te integreren in uw toepassingen. De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Deze bevat meer aantekeningen en functies.

## <a name="prerequisites"></a>Vereisten

- [Node.js](https://www.nodejs.org/)

Een console toepassing instellen met behulp van de Bing Video Search-client bibliotheek:
* Voer `npm install ms-rest-azure` uit in uw ontwikkelomgeving.
* Voer `npm install azure-cognitiveservices-videosearch` uit in uw ontwikkelomgeving.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Java script-bestand in uw favoriete IDE of editor en voeg `require()` een instructie toe voor de Bing Video Search-client `CognitiveServicesCredentials` bibliotheek en-module. Maak een variabele voor uw abonnementssleutel. 
    
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
> [Een web-app met één pagina maken](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zie ook 

* [Wat is de Bing Video's zoeken-API?](../../overview.md)
* [Voor beelden van cognitieve Services .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)