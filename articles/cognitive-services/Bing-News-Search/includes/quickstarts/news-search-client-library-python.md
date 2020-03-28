---
title: Bing News Search Python-clientbibliotheek snel aan de slag
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: c1bd0d86a3fd9d19d67d84b9b05955421373e01e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503879"
---
Gebruik deze snelle start om te beginnen met het zoeken naar nieuws met de Bing News Search-clientbibliotheek voor Python. Hoewel Bing News Search een REST API heeft die compatibel is met de meeste programmeertalen, biedt de clientbibliotheek een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py)

## <a name="prerequisites"></a>Vereisten

* [Python (Python)](https://www.python.org/) 2.x of 3.x

U kunt het best een [virtuele omgeving](https://docs.python.org/3/tutorial/venv.html) gebruiken voor uw Python-ontwikkeling. U kunt de virtuele omgeving installeren en initialiseren met de [venv-module](https://pypi.python.org/pypi/virtualenv). U moet een virtualenv voor Python 2.7 installeren. U kunt een virtuele omgeving maken met:

```console
python -m venv mytestenv
```

Met deze opdracht u de afhankelijkheden van de Bing News Search-clientbibliotheek installeren:
    
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
> [Een web-app voor één pagina maken](../../tutorial-bing-news-search-single-page-app.md)
