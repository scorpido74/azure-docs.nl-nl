---
title: 'Snelstartgids: Image Insights ophalen met behulp van de REST API en python-Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Leer hoe u een afbeelding uploadt naar de Bing Visual Search-API en inzichten in de afbeelding verkrijgt.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.openlocfilehash: 7b33a857953b7f96180e306195dd0e8b21450556
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83874005"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Snelstartgids: Image Insights ophalen met behulp van de Bing Visual Search REST API en python

Gebruik deze Quick Start om uw eerste oproep naar de Bing Visual Search-API te maken. Deze python-toepassing uploadt een installatie kopie naar de API en geeft de informatie weer die wordt geretourneerd. Hoewel deze toepassing is geschreven in Python, is de API een REST-webservice die compatibel is met de meeste programmeer talen.

## <a name="prerequisites"></a>Vereisten

* [Python 3. x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>De toepassing initialiseren

1. Maak een nieuw python-bestand in uw favoriete IDE of editor en voeg de volgende `import` instructie toe:

    ```python
    import requests, json
    ```

2. Maak variabelen voor de abonnementssleutel, het eindpunt en het pad voor de afbeelding die u uploadt. Voor de waarde van `BASE_URI` kunt u het globale eind punt in de volgende code gebruiken of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) eindpunt gebruiken dat wordt weer gegeven in de Azure portal voor uw resource.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
3. Wanneer u een lokale installatie kopie uploadt, moeten de formulier gegevens de `Content-Disposition` koptekst bevatten. Stel de `name` para meter in op "image" en stel de `filename` para meter in op de bestands naam van de installatie kopie. De inhoud van het formulier bevat de binaire gegevens van de installatie kopie. De maximale afbeeldings grootte die u kunt uploaden, is 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

4. Maak een woordenlijst object voor de koptekst gegevens van uw aanvraag. Verbind uw abonnements sleutel met de teken reeks `Ocp-Apim-Subscription-Key` .

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

5. Maak een andere woorden lijst die uw afbeelding bevat, die wordt geopend en geüpload wanneer u de aanvraag verzendt.

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Het JSON-antwoord parseren

Maak een aangeroepen methode `print_json()` voor het accepteren van de API-reactie en druk de JSON af.

```python
def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
```

## <a name="send-the-request"></a>De aanvraag verzenden

Gebruik `requests.post()` om een aanvraag naar de Bing Visual Search-API te verzenden. Neem hierin de tekenreeks voor uw eindpunt, de header en gegevens over het bestand op. Afdrukken `response.json()` met `print_json()` .

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
> [Een Visual Search Web-app met één pagina maken](../tutorial-bing-visual-search-single-page-app.md)
