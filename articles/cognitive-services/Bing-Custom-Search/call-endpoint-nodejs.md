---
title: 'Snelstart: bel uw Bing Custom Search-eindpunt met Node.js | Microsoft Documenten'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om te beginnen met het opvragen van zoekresultaten van uw Bing Custom Search-instantie met behulp van Node.js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 1c7bd97de4e46e1c8da467840006fe2520851caf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238869"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Snelstart: bel uw Bing Custom Search-eindpunt met Node.js

Gebruik deze quickstart om te beginnen met het opvragen van zoekresultaten van uw Bing Aangepaste zoekopdrachten-exemplaar. Hoewel deze toepassing is geschreven in JavaScript, is de Bing Aangepaste zoekopdrachten-API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. De broncode voor dit voorbeeld is beschikbaar op [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js)

## <a name="prerequisites"></a>Vereisten

- Een Bing Custom Search-exemplaar. Zie [Snelstart: Maak uw eerste voorbeeld van Bing Custom Search](quick-start.md) voor meer informatie.

- [Node.js](https://www.nodejs.org/)

- De [JavaScript-aanvragenbibliotheek](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor en voeg een instructie `require()` toe voor de aanvragenbibliotheek. Maak variabelen voor de abonnementssleutel, de aangepaste configuratie-id en een zoekterm. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Een zoekaanvraag verzenden en ontvangen 

1. Maak een variabele voor het opslaan van de gegevens die in uw aanvraag worden verzonden. Stel de aanvraag-URL samen door uw zoekterm toe te voegen aan de queryparameter `q=`, en de aangepaste configuratie-id van uw zoekinstantie aan `customconfig=`. Scheid de parameters van elkaar met een `&`-teken. U het algemene eindpunt hieronder gebruiken of het [aangepaste subdomeineindpunt](../../cognitive-services/cognitive-services-custom-subdomains.md) dat wordt weergegeven in de Azure-portal voor uw bron.

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Gebruik de JavaScript-aanvragenbibliotheek voor het verzenden van een zoekaanvraag naar uw Bing Custom Search-exemplaar en geef informatie weer over de resultaten, waaronder de naam, de URL en de datum waarop de webpagina voor het laatst is verkend.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor aangepaste zoekopdrachten bouwen](./tutorials/custom-search-web-page.md)
