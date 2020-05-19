---
title: 'Snelstartgids: uw Bing Aangepaste zoekopdrachten-eind punt aanroepen met python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Gebruik deze Quick Start om te beginnen met het aanvragen van zoek resultaten van uw Bing Aangepaste zoekopdrachten-exemplaar met behulp van python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: e1248a4e90653f0e862841ec6f58a12364943590
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196564"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Snelstartgids: uw Bing Aangepaste zoekopdrachten-eind punt aanroepen met behulp van python

Gebruik deze Quick Start om te leren hoe u zoek resultaten kunt aanvragen van uw Bing Aangepaste zoekopdrachten-exemplaar. Hoewel deze toepassing is geschreven in Python, is de Bing Custom Search-API een REST-webservice die compatibel is met de meeste programmeer talen. De bron code voor dit voor beeld is beschikbaar op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Vereisten

- Een Bing Custom Search-exemplaar. Zie [Quick Start: uw eerste Bing aangepaste zoekopdrachten-exemplaar maken](quick-start.md)voor meer informatie.
- [Python](https://www.python.org/) 2. x of 3. x.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

- Maak een nieuw Python-bestand in uw favoriete IDE of editor en voeg de volgende importinstructies toe. Maak variabelen voor uw abonnements sleutel, aangepaste configuratie-ID en zoek term.

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Een zoekaanvraag verzenden en ontvangen 

1. Maak de aanvraag-URL door uw zoek term toe te voegen aan de `q=` query parameter en de aangepaste configuratie-id van uw Zoek instantie te koppelen aan de `customconfig=` para meter. Scheid de para meters met een en-teken ( `&` ). U kunt het globale eind punt in de volgende code gebruiken of het [aangepaste subdomein](../../cognitive-services/cognitive-services-custom-subdomains.md) eindpunt gebruiken dat wordt weer gegeven in de Azure portal voor uw resource.

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. De aanvraag verzenden naar uw Bing Aangepaste zoekopdrachten-exemplaar en de geretourneerde Zoek resultaten afdrukken.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor aangepaste zoekopdrachten bouwen](./tutorials/custom-search-web-page.md)
