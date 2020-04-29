---
title: Quick start voor python-client bibliotheek Bing Aangepaste zoekopdrachten
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78252882"
---
Ga aan de slag met de Bing Aangepaste zoekopdrachten-client bibliotheek voor python. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. Met de Bing Custom Search-API kunt u op maat gemaakte, advertenties gratis zoek functies maken voor onderwerpen die u vindt. De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Gebruik de Bing Aangepaste zoekopdrachten-client bibliotheek voor python voor het volgende:
* Zoek resultaten zoeken op Internet vanuit uw Bing Aangepaste zoekopdrachten-exemplaar.

[Reference documentation](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python) | PyPi-voor[beelden](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/) ([Source code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [package)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | voor referentie documentatie


## <a name="prerequisites"></a>Vereisten

- Een Bing Custom Search-exemplaar. Zie [Quick Start: uw eerste Bing aangepaste zoekopdrachten-exemplaar maken](../../quick-start.md) voor meer informatie.
- Python [2.x of 3.x](https://www.python.org/) 
- De [Bing aangepaste zoekopdrachten SDK voor python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>De python-client bibliotheek installeren

Installeer de Bing Aangepaste zoekopdrachten-client bibliotheek met de volgende opdracht.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

Maak een nieuw python-bestand in uw favoriete editor of IDE en voeg de volgende import bewerkingen toe.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Een zoek-client maken en een aanvraag verzenden

1. Maak een variabele voor de abonnements sleutel en het eind punt.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Maak een instantie van `CustomSearchClient`met behulp `CognitiveServicesCredentials` van een object met de abonnements sleutel. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Verzend een zoek opdracht met `client.custom_instance.search()`. Voeg uw zoek term toe aan `query` de para meter en `custom_config` stel deze in op uw aangepaste configuratie-ID om uw zoek exemplaar te gebruiken. U kunt uw ID ophalen via de [Bing aangepaste zoekopdrachten Portal](https://www.customsearch.ai/)door te klikken op het tabblad **productie** .

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>De zoek resultaten weer geven

Als er Zoek resultaten voor webpagina's zijn gevonden, de eerste ophalen en de naam, URL en het totale aantal gevonden webpagina's afdrukken.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor aangepaste zoekopdrachten bouwen](../../tutorials/custom-search-web-page.md)
