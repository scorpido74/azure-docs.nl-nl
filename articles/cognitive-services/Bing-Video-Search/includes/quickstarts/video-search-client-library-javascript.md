---
title: Bing Video Zoeken JavaScript-clientbibliotheek snel aan de slag
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: b642d981b79d13857881ec8cc5796e6365003ace
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289761"
---
Gebruik deze snelstart om te beginnen met zoeken naar nieuws met de Bing Video Search-clientbibliotheek voor JavaScript. Bing Video Search heeft een REST API die compatibel is met de meeste programmeertalen, maar de clientbibliotheek biedt een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) Deze bevat meer aantekeningen en functies.

## <a name="prerequisites"></a>Vereisten

- [Node.js](https://www.nodejs.org/)

Ga als lid van de browser toepassing Bing Video Search als lid van de browser:
* Voer `npm install ms-rest-azure` uit in uw ontwikkelomgeving.
* Voer `npm install azure-cognitiveservices-videosearch` uit in uw ontwikkelomgeving.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete `require()` IDE of editor en voeg `CognitiveServicesCredentials` een instructie toe voor de bing-clientbibliotheek en -module voor Videozoeken. Maak een variabele voor uw abonnementssleutel. 
    
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
* [Cognitieve services .NET SDK-monsters](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)