---
title: 'Snelstart: bel uw Bing Custom Search-eindpunt met Python | Microsoft Documenten'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om te beginnen met het opvragen van zoekresultaten van uw Bing Custom Search-instantie met behulp van Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: a601b309d18e489f6b631cb26e5f3e13ef790b42
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238837"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Snelstart: bel uw Bing Custom Search-eindpunt met Python

Gebruik deze quickstart om te beginnen met het opvragen van zoekresultaten van uw Bing Aangepaste zoekopdrachten-exemplaar. Hoewel deze toepassing is geschreven in Python, is de Bing Custom Search-API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. De broncode voor dit voorbeeld is beschikbaar op [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py)

## <a name="prerequisites"></a>Vereisten

- Een Bing Custom Search-exemplaar. Zie [Snelstart: Maak uw eerste voorbeeld van Bing Custom Search](quick-start.md) voor meer informatie.
- [Python (Python)](https://www.python.org/) 2.x of 3.x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Python-bestand in uw favoriete IDE of editor en voeg de volgende importinstructies toe. Maak variabelen voor de abonnementssleutel, de aangepaste configuratie-id en een zoekterm. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Een zoekaanvraag verzenden en ontvangen 

1. Stel de aanvraag-URL samen door uw zoekterm toe te voegen aan de queryparameter `q=`, en de aangepaste configuratie-id van uw zoekinstantie aan `customconfig=`. Scheid de parameters van elkaar met een `&`-teken. U het algemene eindpunt hieronder gebruiken of het [aangepaste subdomeineindpunt](../../cognitive-services/cognitive-services-custom-subdomains.md) dat wordt weergegeven in de Azure-portal voor uw bron.

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Verstuur de aanvraag naar uw Bing Custom Search-exemplaar en geef de geretourneerde zoekresultaten weer.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor aangepaste zoekopdrachten bouwen](./tutorials/custom-search-web-page.md)
