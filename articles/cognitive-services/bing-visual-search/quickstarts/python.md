---
title: 'Snelstart: krijg beeldinzichten met behulp van de REST API en Python - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Leer hoe u een afbeelding uploadt naar de Bing Visual Search-API en inzichten in de afbeelding verkrijgt.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: b56f6743b642904349797ac5b6167194f7916b45
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446592"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Snelstart: krijg beeldinzichten met de Bing Visual Search REST API en Python

Gebruik deze snelstart om uw eerste gesprek te voeren naar de Bing Visual Search API en de resultaten weer te geven. Deze Python-toepassing uploadt een afbeelding naar de API en geeft de informatie weer die wordt geretourneerd. Hoewel deze toepassing is geschreven in Python, is de API een RESTful Web-service die compatibel is met de meeste programmeertalen.

## <a name="prerequisites"></a>Vereisten

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>De toepassing initialiseren

1. Maak een nieuw Python-bestand in uw favoriete IDE of editor en voeg de volgende `import` instructie toe:

    ```python
    import requests, json
    ```

2. Maak variabelen voor de abonnementssleutel, het eindpunt en het pad voor de afbeelding die u uploadt. `BASE_URI`kan het algemene eindpunt hieronder zijn of het [aangepaste eindpunt voor subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) dat wordt weergegeven in de Azure-portal voor uw bron:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
    Wanneer u een lokale afbeelding uploadt, `Content-Disposition` moeten de formuliergegevens de koptekst bevatten. U moet `name` de parameter instellen op 'afbeelding' en u de `filename` parameter instellen op elke tekenreeks. De inhoud van het formulier bevat de binaire gegevens van de afbeelding. De maximale afbeeldingsgrootte die u uploaden is 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

3. Maak een woordenboekobject om de koptekstgegevens van uw aanvraag vast te houden. Bind uw abonnementssleutel `Ocp-Apim-Subscription-Key`aan de tekenreeks, zoals hieronder wordt weergegeven:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Maak een ander woordenboek met uw afbeelding, die wordt geopend en geüpload wanneer u het verzoek verzendt:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Het JSON-antwoord parseren

1. Maak een `print_json()` methode die moet worden aangeroepen om de API-respons op te nemen en druk de JSON af:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>De aanvraag verzenden

1. Gebruik `requests.post()` om een aanvraag naar de Bing Visual Search-API te verzenden. Neem hierin de tekenreeks voor uw eindpunt, de header en gegevens over het bestand op. Afdrukken `response.json()` `print_json()`met :

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor visueel zoeken met één pagina maken](../tutorial-bing-visual-search-single-page-app.md)
