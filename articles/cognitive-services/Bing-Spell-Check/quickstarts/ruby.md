---
title: 'Snelstart: Spelling controleren met de REST API en Ruby - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de Bing Spell Check REST API om spelling en grammatica te controleren met deze quickstart.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 89a2a345e2a4e3ca1be31297e614e86f800e6316
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448419"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Snelstart: spelling controleren met de Bing Spell Check REST API en Ruby

Gebruik deze quickstart om uw eerste aanroep naar de Bing Spellingcontrole-REST API te maken met behulp van Ruby. Deze eenvoudige toepassing verzendt een aanvraag naar de API en retourneert een lijst met woorden die niet werden herkend, gevolgd door voorgestelde correcties. Hoewel deze toepassing in Ruby is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. De broncode voor deze toepassing is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Vereisten

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) of hoger.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Ruby-bestand in uw favoriete editor of IDE en voeg de volgende vereisten toe. 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Maak variabelen voor uw abonnementssleutel, eindpunt-URI en pad. Maak uw aanvraagparameters door de parameter `mkt=` toe te voegen aan uw markt en `&mode` aan de `proof`-controlemodus. U het algemene eindpunt hieronder gebruiken of het [aangepaste subdomeineindpunt](../../../cognitive-services/cognitive-services-custom-subdomains.md) dat wordt weergegeven in de Azure-portal voor uw bron.

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Een spellingcontroleaanvraag verzenden

1. Maak een URI op basis van uw host-URI, pad en parametertekenreeks. Stel de query in op de tekst die u wilt spellen.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Maak een aanvraag met behulp van de URI die hierboven is gemaakt. Voeg uw sleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Verzend de aanvraag.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Haal het JSON-antwoord op en druk het af naar de console. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

Bouw en voer uw project uit.

Als u de opdrachtregel gebruikt, gebruikt u de volgende opdracht om de toepassing uit te voeren.

```bash
ruby <FILE_NAME>.rb
```

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../tutorials/spellcheck.md)

- [Wat is de Bing Spellingcontrole-API?](../overview.md)
- [Referentie voor de Bing Spellingcontrole-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
