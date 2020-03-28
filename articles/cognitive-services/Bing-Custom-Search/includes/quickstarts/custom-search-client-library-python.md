---
title: Bing Custom Search Python-clientbibliotheek snel aan de slag
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78252882"
---
Ga aan de slag met de Bing Custom Search-clientbibliotheek voor Python. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit. Met de Bing Custom Search API u op maat gemaakte, advertentievrije zoekervaringen maken voor onderwerpen die u belangrijk vindt. De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py)

Gebruik de bing Custom Search-clientbibliotheek voor Python om:
* Zoek zoekresultaten op internet in uw instantie Bing Custom Search.

[Voorbeeld van broncode](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python) |  | van[naslagdocumentatieBibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch)[(PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [Samples](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Vereisten

- Een Bing Custom Search-exemplaar. Zie [Snelstart: Maak uw eerste voorbeeld van Bing Custom Search](../../quick-start.md) voor meer informatie.
- Python [2.x of 3.x](https://www.python.org/) 
- De [Bing Custom Search SDK voor Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>De Python-clientbibliotheek installeren

Installeer de bing Custom Search-clientbibliotheek met de volgende opdracht.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

Maak een nieuw Python-bestand in uw favoriete editor of IDE en voeg de volgende importen toe.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Een zoekclient maken en een aanvraag verzenden

1. Maak een variabele voor uw abonnementssleutel en eindpunt.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Een instantie `CustomSearchClient`van , `CognitiveServicesCredentials` maken met behulp van een object met de abonnementssleutel. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Stuur een zoekverzoek met `client.custom_instance.search()`. Sluit uw zoekterm `query` toe aan `custom_config` de parameter en stel uw aangepaste configuratie-id in om uw zoekexemplaar te gebruiken. U uw id ophalen via de [bing-portal voor aangepast zoeken](https://www.customsearch.ai/)door op het tabblad **Productie** te klikken.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Bekijk de zoekresultaten

Als er zoekresultaten van webpagina's zijn gevonden, krijgt u de eerste en drukt u de gevonden naam, URL en totaal aantal webpagina's af.

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
