---
title: 'Snelstartgids: een zoek opdracht naar de REST API verzenden met behulp van Ruby-Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om een aanvraag naar de REST API van Bing Entity Search te verzenden via Ruby en een JSON-antwoord te ontvangen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 69e4d992e2ef89b4d3d9408d6e50591fb8166c79
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385776"
---
# <a name="quickstart-for-bing-entity-search-api-with-ruby"></a>Snelstartgids: Bing Entiteiten zoeken-API met Ruby

Gebruik deze quickstart om voor het eerst de Bing Entity Search REST-API aan te roepen en het JSON-antwoord te bekijken. Met deze eenvoudige Ruby-toepassing wordt een query naar de API gestuurd om nieuws te zoeken en wordt het antwoord weergegeven. De broncode voor deze toepassing is beschikbaar [op GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingEntitySearchv7.rb).

Hoewel deze toepassing in Ruby is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

## <a name="prerequisites"></a>Vereisten

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) of later.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak in uw favoriete IDE of teksteditor een nieuw Ruby-bestand en importeer de volgende pakketten.

    ```ruby
    require 'net/https'
    require 'cgi'
    require 'json'
    ```

2. Maak variabelen voor het API-eindpunt, de Nieuws zoeken-URL, de abonnementssleutel en een zoekquery. U kunt het volgende globale eind punt gebruiken of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) -eind punt dat wordt weer gegeven in de Azure portal voor uw resource.
    
    ```ruby
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

## <a name="format-and-make-an-api-request"></a>Een API-aanvraag opmaken en maken

1. Maak de parametertekenreeks voor de aanvraag door uw marktwaarde toe te voegen aan de parameter `?mkt=`. Codeer de query en voeg deze toe aan de parameter `&q=`. Combineer de API-host, het pad en de parameters voor de aanvraag, en cast ze als een URI-object.

    ```ruby
    params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
    uri = URI (host + path + params)
    ```

2. Gebruik de variabelen uit de laatste stap om de aanvraag te maken. Voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

3. De aanvraag verzenden en het antwoord afdrukken

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end

    puts JSON::pretty_generate (JSON (response.body))
    ```

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app van één pagina maken](../tutorial-bing-entities-search-single-page-app.md).

* [Wat is de Bing Entiteiten zoeken-API?](../search-the-web.md)
* [Naslaghandleiding Bing Entiteiten zoeken-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
