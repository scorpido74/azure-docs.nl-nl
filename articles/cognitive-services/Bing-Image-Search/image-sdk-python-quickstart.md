---
title: 'Snelstartgids: zoeken naar afbeeldingen-Bing Image Search SDK voor python'
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstartgids om voor de eerste keer afbeeldingen te zoeken met behulp van de Bing Image Search SDK, wat een wrapper is voor de API en die dus dezelfde functies bevat. Deze eenvoudige Python-toepassing verzendt een zoekquery voor afbeeldingen, parseert het JSON-antwoord en geeft de URL weer van de eerst geretourneerde afbeelding.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 561162767a48a060763510310de77767f37d4eb4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770183"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-python"></a>Snelstart: Zoeken naar afbeeldingen met de Bing Afbeeldingen zoeken-SDK voor Python

Gebruik deze snelstartgids om voor de eerste keer afbeeldingen te zoeken met behulp van de Bing Image Search SDK, wat een wrapper is voor de API en die dus dezelfde functies bevat. Deze eenvoudige Python-toepassing verzendt een zoekquery voor afbeeldingen, parseert het JSON-antwoord en geeft de URL weer van de eerst geretourneerde afbeelding.

De broncode voor dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) met extra foutafhandeling en aantekeningen.

## <a name="prerequisites"></a>Vereisten

* [Python 2.7 of 3.4](https://www.python.org/) en hoger.

* De [Azure Afbeeldingen zoeken-SDK](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) voor Python
    * Installeren met behulp van `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Python-script in uw favoriete IDE of editor en importeer het volgende:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Maak variabelen voor uw abonnementssleutel en zoekterm.

    ```python
    subscription_key = "Enter your key here"
    subscription_endpoint = "Enter your endpoint here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>De zoekclient voor afbeeldingen maken

1. Maak een exemplaar van `CognitiveServicesCredentials` en gebruik deze om een exemplaar van de client te maken:

    ```python
    client = ImageSearchClient(endpoint=subscription_endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```
1. Verzend een zoekquery naar de Bing Afbeeldingen zoeken-API:
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>De resultaten verwerken en weergeven

Parseer de afbeeldingsresultaten die in het antwoord zijn geretourneerd.


Als het antwoord zoekresultaten bevat, wordt het eerste resultaat opgeslagen en worden de bijbehorende gegevens weergegeven, zoals een miniatuur-URL en de oorspronkelijke URL, samen met het totale aantal geretourneerde afbeeldingen.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie voor app met één pagina voor Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Zie ook

* [Wat is Bing Image Search?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Online interactieve demo proberen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Gratis Cognitive Services-toegangssleutel ophalen](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Python-voorbeelden voor de Azure Cognitive Services-SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Documentatie van Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Naslag voor Bing Afbeeldingen zoeken-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
