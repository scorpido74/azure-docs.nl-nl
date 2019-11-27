---
title: 'Quick Start: een zoek opdracht voor nieuws uitvoeren met de SDK voor python-Bing News Search'
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstartgids om nieuws te zoeken met de Bing News Search-SDK voor Python en om het antwoord te verwerken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2f3d8c88e7949265f12405760acbccebb89d1df5
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379062"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Quick Start: een zoek opdracht voor nieuws uitvoeren met de Bing News Search SDK voor python

Gebruik deze quickstart om aan de slag te gaan met de Bing Nieuws zoeken-API voor Python om nieuws te zoeken. Hoewel Bing Nieuws zoeken een REST API heeft die compatibel is met de meeste programmeertalen, biedt de SDK een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Vereisten

* [Python](https://www.python.org/) 2.x of 3.x

U kunt het best een [virtuele omgeving](https://docs.python.org/3/tutorial/venv.html) gebruiken voor uw Python-ontwikkeling. U kunt de virtuele omgeving installeren en initialiseren met de [venv-module](https://pypi.python.org/pypi/virtualenv). U moet een virtualenv voor Python 2.7 installeren. U kunt een virtuele omgeving maken met:

```console
python -m venv mytestenv
```

U kunt de Bing News Search SDK-afhankelijkheden installeren met deze opdracht:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Python-bestand in uw favoriete IDE of editor en importeer de volgende bibliotheken. Maak een variabele voor uw abonnementssleutel en een voor uw zoekterm.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>De client initialiseren en een aanvraag verzenden

1. Maak een instantie van `CognitiveServicesCredentials`. Maak een instantie van de client:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Verzend een zoekquery naar de Nieuws zoeken-API en sla het antwoord op.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Het antwoord parseren

Als er zoekresultaten worden gevonden, geef dan het eerste webpaginaresultaat weer:

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](tutorial-bing-news-search-single-page-app.md)
