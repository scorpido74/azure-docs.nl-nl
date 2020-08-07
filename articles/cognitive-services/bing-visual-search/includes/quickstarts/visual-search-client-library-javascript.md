---
title: Quickstart voor Bing Visual Search-clientbibliotheek voor JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: 3a6c89c3932adb4f9465172ca64b9356db1f624a
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406997"
---
In deze quickstart leert u hoe u met de JavaScript-clientbibliotheek afbeeldingsinzichten krijgt uit de Bing Visual Search-service. Hoewel Bing Visual Search een REST API heeft die compatibel is met de meeste programmeertalen, biedt de clientbibliotheek een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

[Referentiedocumentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-visualsearch/?view=azure-node-latest) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-visualsearch) | [Pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Vereisten
* [Node.js](https://www.nodejs.org/)
* De Bing Visual Search-clientbibliotheek voor JavaScript
    * U stelt een consoletoepassing in met behulp van de Bing Visual Search-clientbibliotheek met de volgende opdrachten:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-visualsearch`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor en voeg de volgende vereisten toe. Maak vervolgens variabelen voor de abonnementssleutel , de aangepaste configuratie-id en het bestandspad naar de afbeelding die u wilt uploaden. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Maak een instantie van de client.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Zoeken naar afbeeldingen

1. Gebruik `fs.createReadStream()` om uw afbeeldingsbestand in te lezen en variabelen te maken voor uw zoekaanvraag en -resultaten. Gebruik vervolgens de client om te zoeken naar afbeeldingen.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. De resultaten van de vorige query parseren:

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app van één pagina maken](../../tutorial-bing-visual-search-single-page-app.md).
