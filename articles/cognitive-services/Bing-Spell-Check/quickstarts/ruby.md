---
title: 'Snelstartgids: spelling controleren met de REST API en Ruby-Bing Spellingcontrole'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de Bing Spellingcontrole REST API om de spelling en grammatica te controleren met deze Snelstartgids.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: 9f5f841bd7fd33d4d6c7dcd1a1f7ab754610b973
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869892"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Snelstartgids: spelling controleren met de Bing Spellingcontrole REST API en ruby

Gebruik deze quickstart om uw eerste aanroep naar de Bing Spellingcontrole-REST API te maken met behulp van Ruby. Met deze eenvoudige toepassing wordt een aanvraag naar de API verzonden en wordt een lijst met voorgestelde correcties geretourneerd. 

Hoewel deze toepassing wordt geschreven in Ruby, is de API een REST-webservice die compatibel is met de meeste programmeer talen. De broncode voor deze toepassing is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Vereisten

* [Ruby 2,4](https://www.ruby-lang.org/en/downloads/) of hoger.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw ruby-bestand in uw favoriete editor of IDE en voeg de volgende vereisten toe: 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Maak variabelen voor uw abonnements sleutel, eind punt-URI en pad. U kunt het globale eind punt in de volgende code gebruiken of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) eindpunt gebruiken dat wordt weer gegeven in de Azure portal voor uw resource. Uw aanvraag parameters maken:

   a. Wijs uw markt code toe aan de `mkt` para meter met de `=` operator. De markt code is de code van het land of de regio waarvan u de aanvraag maakt. 

   b. Voeg de `mode` para meter met de `&` operator toe en wijs vervolgens de spelling controle modus toe. De modus kan ofwel `proof` (de meeste spelling-en grammatica fouten) of `spell` (de meeste spel fouten worden onderschept, maar niet zo veel grammatica fouten). 

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Een spellingcontroleaanvraag verzenden

1. Maak een URI op basis van uw host-URI, pad en parametertekenreeks. Stel de query in op de tekst waarvoor u de spelling controle wilt uitvoeren.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Maak een aanvraag met behulp van de URI die u eerder hebt gemaakt. Voeg uw sleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

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

Uw project bouwen en uitvoeren. Als u de opdracht regel gebruikt, gebruikt u de volgende opdracht om de toepassing uit te voeren:

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
- [Naslag informatie over Bing Spellingcontrole-API V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
