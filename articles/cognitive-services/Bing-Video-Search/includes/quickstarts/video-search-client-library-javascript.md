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
ms.custom: devx-track-js
ms.openlocfilehash: fa1ecb24649d9355e6104bff8add5430fdd64c3c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377052"
---
Gebruik deze quickstart om aan de slag te gaan met de Bing Video Search-clientbibliotheek voor JavaScript om nieuws te zoeken. Hoewel Bing Video Search een REST API heeft die compatibel is met de meeste programmeertalen, biedt de clientbibliotheek een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Deze bevat meer aantekeningen en functies.

## <a name="prerequisites"></a>Vereisten

* Nieuwste versie van [Node.js](https://nodejs.org/en/download/).
* De [Bing Video Search-SDK voor JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-videosearch)
     *  Voer `npm install @azure/cognitiveservices-videosearch` uit om deze te installeren
* De klasse `CognitiveServicesCredentials` van het pakket `@azure/ms-rest-azure-js` om de client te verifiëren.
     * Voer `npm install @azure/ms-rest-azure-js` uit om deze te installeren

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor en voeg een instructie `require()` toe voor de Bing Video's zoeken-bibliotheek en `CognitiveServicesCredentials`-module. Maak een variabele voor uw abonnementssleutel. 
    
    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('@azure/cognitiveservices-videosearch');
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