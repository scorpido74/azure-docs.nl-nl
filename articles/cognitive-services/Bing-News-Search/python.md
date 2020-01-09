---
title: 'Snelstartgids: een zoek opdracht voor nieuws met python en de Bing News Search uitvoeren REST API'
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstartgids om een aanvraag naar de REST API van Bing News Search te verzenden via Python en een JSON-antwoord te ontvangen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 1c424c75a4df193ec412355607c68abeda0560a5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448498"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Snelstartgids: een zoek opdracht uitvoeren met behulp van python en de Bing News Search REST API

Gebruik deze quickstart om voor het eerst de Bing Nieuws zoeken-API aan te roepen en een JSON-antwoord te ontvangen. Met deze eenvoudige JavaScript-toepassing wordt een zoekquery naar de API verzonden en worden de resultaten verwerkt. Hoewel deze toepassing in Python is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

U kunt dit codevoorbeeld uitvoeren als een Jupyter-notebook op [MyBinder](https://mybinder.org) door te klikken op de badge launch binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

De broncode voor dit voorbeeld is ook beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Python-bestand in uw favoriete IDE of editor en importeer de aanvraagmodule. Maak variabelen voor uw abonnementssleutel, eindpunt en zoekterm. U kunt het volgende globale eind punt gebruiken of het [aangepaste subdomein](../../cognitive-services/cognitive-services-custom-subdomains.md) -eind punt dat wordt weer gegeven in de Azure portal voor uw resource.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Parameters voor de aanvraag maken

1. Voeg uw abonnementssleutel toe aan een nieuwe woordenlijst en gebruik `"Ocp-Apim-Subscription-Key"` daarbij als sleutel. Doe hetzelfde voor uw zoekparameters.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Een aanvraag verzenden en een antwoord ontvangen

1. Gebruik de aanvraagbibliotheek om de Bing Visual Search-API aan te roepen met uw abonnementssleutel, net als de woordenlijstobjecten die u tijdens de laatste stap hebt gemaakt.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` bevat het antwoord van de API als JSON-object. Open de beschrijvingen van de artikelen in het antwoord.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>De resultaten weergeven

Deze beschrijvingen kunnen vervolgens worden weergegeven als een tabel met het zoekwoord **vet** gemarkeerd.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](tutorial-bing-news-search-single-page-app.md)
