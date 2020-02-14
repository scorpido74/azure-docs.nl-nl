---
title: "Snelstartgids: zoeken naar Video's met behulp van de SDK voor python-Bing Video Search"
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om zoekaanvragen voor video's te verzenden naar de Bing Video Search-SDK voor Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: aahi
ms.openlocfilehash: 9d7b2a8950134e530e042e862a1c19abe89fd78d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201217"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>Snelstartgids: een video zoekopdracht uitvoeren met de Bing Video Search SDK voor python

Gebruik deze quickstart om nieuws te gaan zoeken met de Bing Video Search-SDK voor Python. Hoewel Bing Video Search een REST API heeft die compatibel is met de meeste programmeertalen, biedt de SDK een eenvoudige manier om de service in uw toepassingen te integreren. De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) met aanvullende aantekeningen en functies.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Vereisten

- [Python](https://www.python.org/) 2.x of 3.x
- De Bing Video Search-SDK voor Python

U kunt het best een [virtuele omgeving](https://docs.python.org/3/tutorial/venv.html) van Python gebruiken. U kunt een virtuele omgeving installeren en initialiseren met de [venv-module](https://pypi.python.org/pypi/virtualenv). Installeer virtualenv voor Python 2.7 met:

```console
python -m venv mytestenv
```

Installeer de Bing Video Search-SDK met:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Python-bestand in uw favoriete IDE of editor en voeg de volgende importinstructies toe. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchClient
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Maak een variabele voor uw abonnementssleutel. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    ```

## <a name="create-the-search-client"></a>De zoekclient maken

Maak een instantie van de `CognitiveServicesCredentials` en instantieer de client:

```python
client = VideoSearchAPI(endpoint, CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Een zoekaanvraag verzenden en een antwoord ontvangen

1. Gebruik `client.videos.search()` met uw zoekopdracht om een aanvraag te verzenden naar de Bing Video's zoeken-API en een antwoord terug te krijgen.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Als het antwoord zoekresultaten bevat, haalt u het eerste resultaat op, en geeft u de bijbehorende id, naam en URL weer.

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zie ook 

- [Wat is de Bing Video's zoeken-API?](../overview.md)
- [Voorbeelden voor Cognitive Services .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
