---
title: Bing Video Search Python-clientbibliotheek snel aan de slag
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: 7a9fab8ba8bb9d21c9284cbf14bc67226d2ef9d3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289740"
---
Gebruik deze snelle start om te beginnen met het zoeken naar nieuws met de Bing Video Search-clientbibliotheek voor Python. Bing Video Search heeft een REST API die compatibel is met de meeste programmeertalen, maar de clientbibliotheek biedt een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) met extra annotaties en functies.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Vereisten

- [Python (Python)](https://www.python.org/) 2.x of 3.x
- De Bing Video Search-clientbibliotheek voor python

Het wordt aanbevolen dat u een python [virtuele omgeving](https://docs.python.org/3/tutorial/venv.html)te gebruiken. U een virtuele omgeving installeren en initialiseren met de [venv-module.](https://pypi.python.org/pypi/virtualenv) Installeer virtualenv voor Python 2.7 met:

```console
python -m venv mytestenv
```

Installeer de bing-clientbibliotheek voor zoeken in video met:

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
> [Een web-app met één pagina maken](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zie ook 

- [Wat is de Bing Video's zoeken-API?](../../overview.md)
- [Cognitieve services .NET SDK-monsters](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
