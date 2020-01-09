---
title: 'Snelstartgids: zoeken naar entiteiten met de SDK voor python-Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Gebruik deze Snelstartgids om entiteiten te zoeken met de Bing Entity Search SDK voor python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 33ab516b5b501a79ba84c5aba9c3231634f3c662
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448696"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Snelstart: Bing Entiteiten zoeken-SDK met Python

Gebruik deze quickstart om te zoeken naar entiteiten met de Bing Entiteiten zoeken-SDK voor Python. Hoewel Bing Entiteiten zoeken een REST-API heeft die compatibel is met de meeste moderne programmeertalen, biedt de SDK een eenvoudige manier om de service te integreren in uw toepassingen. De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Vereisten

* Python [2.x of 3.x](https://www.python.org/)

* De [Bing Entiteiten zoeken-SDK voor Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

U kunt het best een virtuele Python-omgeving gebruiken. U kunt een virtuele omgeving installeren en initialiseren met de venv-module. U kunt virtualenv installeren met:

```Console
python -m venv mytestenv
```

Installeer de Bing Entiteiten zoeken-SDK met:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Python-bestand in uw favoriete IDE of editor en voeg de volgende importinstructies toe. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Maak een variabele voor uw abonnementssleutel en start een instantie van de client door er een nieuw `CognitiveServicesCredentials`-object mee te maken.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Een zoekopdracht verzenden en een antwoord ontvangen

1. Verzend een zoekopdracht naar Bing Entiteiten zoeken met `client.entities.search()` en een zoekquery. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Als er entiteiten worden geretourneerd, zet u `entity_data.entities.value` om in een lijst en geeft u het eerste resultaat weer.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../tutorial-bing-entities-search-single-page-app.md)

* [Wat is de Bing Entiteiten zoeken-API?](../overview.md )