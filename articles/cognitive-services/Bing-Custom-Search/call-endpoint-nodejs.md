---
title: 'Quick Start: uw Bing Aangepaste zoekopdrachten-eind punt aanroepen met behulp van node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Gebruik deze Quick Start om te beginnen met het aanvragen van zoek resultaten van uw Bing Aangepaste zoekopdrachten-exemplaar met behulp van node. js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: 34d64db9caefd26adc91471ed67e528a6e3196dc
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199847"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Snelstartgids: uw Bing Aangepaste zoekopdrachten-eind punt aanroepen met behulp van node. js

Gebruik deze Quick Start om te leren hoe u zoek resultaten kunt aanvragen van uw Bing Aangepaste zoekopdrachten-exemplaar. Hoewel deze toepassing wordt geschreven in Java script, is het Bing Custom Search-API een REST-webservice die compatibel is met de meeste programmeer talen. De bron code voor dit voor beeld is beschikbaar op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Vereisten

- Een Bing Custom Search-exemplaar. Zie [Quick Start: uw eerste Bing aangepaste zoekopdrachten-exemplaar maken](quick-start.md)voor meer informatie.

- [De node. js Java Script-runtime](https://www.nodejs.org/).

- De [Java script-aanvraag bibliotheek](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

- Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor en voeg een instructie `require()` toe voor de aanvragenbibliotheek. Maak variabelen voor uw abonnements sleutel, aangepaste configuratie-ID en zoek term.

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Een zoekaanvraag verzenden en ontvangen 

1. Maak een variabele voor het opslaan van de gegevens die in uw aanvraag worden verzonden. Maak de aanvraag-URL door uw zoek term toe te voegen aan de `q=` query parameter en de aangepaste configuratie-id van uw Zoek instantie te koppelen aan de `customconfig=` para meter. Scheid de para meters met een en-teken ( `&` ). U kunt het globale eind punt in de volgende code gebruiken of het [aangepaste subdomein](../../cognitive-services/cognitive-services-custom-subdomains.md) eindpunt gebruiken dat wordt weer gegeven in de Azure portal voor uw resource.

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

1. Gebruik de Java script-aanvraag bibliotheek om een zoek opdracht naar uw Bing Aangepaste zoekopdrachten-exemplaar te verzenden en om informatie over de resultaten af te drukken, met inbegrip van de naam, de URL en de datum waarop de webpagina voor het laatst is verkend.

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
