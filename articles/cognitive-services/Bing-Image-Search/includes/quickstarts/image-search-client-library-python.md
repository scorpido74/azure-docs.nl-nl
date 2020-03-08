---
title: Quick start voor python-client bibliotheek Bing Image Search
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: e3dc3fd30d1eceab4b24b6699dd81a91bca51115
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899592"
---
Gebruik deze Quick Start om ervoor te zorgen dat uw eerste installatie kopie wordt doorzocht met behulp van de Bing Image Search-client bibliotheek. Dit is een wrapper voor de API en bevat dezelfde functies. Deze eenvoudige Python-toepassing verzendt een zoekquery voor afbeeldingen, parseert het JSON-antwoord en geeft de URL weer van de eerst geretourneerde afbeelding.

De broncode voor dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) met extra foutafhandeling en aantekeningen.

## <a name="prerequisites"></a>Vereisten

* [Python 2.7 of 3.4](https://www.python.org/) en hoger.

* De [Azure afbeeldingen zoeken-client bibliotheek](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) voor python
    * Installeren met behulp van `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

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
