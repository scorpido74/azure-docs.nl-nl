---
title: Quick start voor python-client bibliotheek Bing News Search
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: c1bd0d86a3fd9d19d67d84b9b05955421373e01e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503879"
---
Gebruik deze Quick Start om te beginnen met zoeken naar nieuws met de Bing News Search-client bibliotheek voor python. Hoewel Bing News Search een REST API compatibel met de meeste programmeer talen heeft, biedt de client bibliotheek een eenvoudige manier om de service te integreren in uw toepassingen. De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Vereisten

* [Python](https://www.python.org/) 2. x of 3. x

U kunt het best een [virtuele omgeving](https://docs.python.org/3/tutorial/venv.html) gebruiken voor uw Python-ontwikkeling. U kunt de virtuele omgeving installeren en initialiseren met de [venv-module](https://pypi.python.org/pypi/virtualenv). U moet een virtualenv voor Python 2.7 installeren. U kunt een virtuele omgeving maken met:

```console
python -m venv mytestenv
```

U kunt de Bing News Search-client bibliotheek afhankelijkheden met de volgende opdracht installeren:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Python-bestand in uw favoriete IDE of editor en importeer de volgende bibliotheken. Maak een variabele voor uw abonnementssleutel en een voor uw zoekterm.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>De client initialiseren en een aanvraag verzenden

1. Maak een instantie van `CognitiveServicesCredentials`.
    
    ```python
    client = NewsSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
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
> [Een web-app met één pagina maken](../../tutorial-bing-news-search-single-page-app.md)
