---
title: Quickstart voor Bing Video Search-clientbibliotheek voor JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: 7cf28df4f009b017699c926d1ca54b7e5320a179
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87404108"
---
Gebruik deze quickstart om aan de slag te gaan met de Bing Video Search-clientbibliotheek voor JavaScript om nieuws te zoeken. Hoewel Bing Video Search een REST API heeft die compatibel is met de meeste programmeertalen, biedt de clientbibliotheek een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Deze bevat meer aantekeningen en functies.

## <a name="prerequisites"></a>Vereisten

- [Node.js](https://www.nodejs.org/)

Stel een consoletoepassing in met de Bing Video Search-clientbibliotheek:
* Voer `npm install ms-rest-azure` uit in uw ontwikkelomgeving.
* Voer `npm install azure-cognitiveservices-videosearch` uit in uw ontwikkelomgeving.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor en voeg een instructie `require()` toe voor de Bing Video's zoeken-bibliotheek en `CognitiveServicesCredentials`-module. Maak een variabele voor uw abonnementssleutel. 
    
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
* [Voorbeelden voor Cognitive Services .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)