---
title: 'Snelstartgids: spelling controleren met de REST API en python-Bing Spellingcontrole'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de Bing Spellingcontrole REST API om de spelling en grammatica te controleren met deze Snelstartgids.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 3350ff4bc810666f7b772607c2983d86902f5ffc
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744050"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Snelstartgids: spelling controleren met de Bing Spellingcontrole REST API en python

Gebruik deze quickstart om uw eerste aanroep naar de Bing Spellingcontrole REST API te maken. Deze eenvoudige Python-toepassing verzendt een aanvraag naar de API en retourneert een lijst met voorgestelde correcties. Hoewel deze toepassing in Python is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. De broncode voor deze toepassing is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Vereisten

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>De toepassing initialiseren

1. Maak een nieuw Python-bestand in uw favoriete IDE of editor en voeg de volgende importinstructie toe.

   ```python
   import requests
   import json
   ```

2. Maak variabelen voor de tekst waarvan u de spelling wilt controleren, maak de abonnementssleutel en het Bing Spellingcontrole-eindpunt. U kunt het volgende globale eind punt gebruiken of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) -eind punt dat wordt weer gegeven in de Azure portal voor uw resource.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Parameters voor de aanvraag maken

1. Een nieuwe woordenlijst maken met `text` als sleutel en uw tekst als waarde.

    ```python
    data = {'text': example_text}
    ```

2. Voeg de parameters voor uw aanvraag toe. Voeg uw markt code toe na `mkt=` . De markt code is het land/de regio waarin u de aanvraag maakt. Voeg ook de modus voor spelling controle toe `&mode=` . Modus (de `proof` meeste spelling-en grammatica fouten worden onderschept) of `spell` (de meeste spelling wordt niet zo veel grammatica fouten onderschept).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Voeg een `Content-Type`-header toe en voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>De aanvraag verzenden en het antwoord lezen

1. Verzend de POST-aanvraag met de aanvraagbibliotheek.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Haal het JSON-antwoord op en druk het af.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>De toepassing uitvoeren

Als u de opdracht regel gebruikt, gebruikt u de volgende opdracht om de toepassing uit te voeren.

```bash
python <FILE_NAME>.py
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
