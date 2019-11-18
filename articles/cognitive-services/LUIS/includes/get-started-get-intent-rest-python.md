---
title: Doel intentie met REST-aanroep in python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: b2c840b9de25ff6997037c284c60390e7afa03ec
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125465"
---
## <a name="prerequisites"></a>Vereisten

* [Python 3.6](https://www.python.org/downloads/) of later.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="get-luis-key"></a>LUIS-sleutel ophalen

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent--programmatically"></a>De intentie programmatisch ophalen

Gebruik python voor het uitvoeren van een query op de [API](https://aka.ms/luis-apim-v3-prediction) prediction-eind punt Get om het Voorspellings resultaat te verkrijgen.

1. Kopieer een van de volgende codefragmenten naar een bestand met de naam `predict.py`:

    ```python
    ########### Python 3.6 #############
    import requests
    
    try:
    
        key = 'YOUR-KEY'
        endpoint = 'YOUR-ENDPOINT' # such as 'westus2.api.cognitive.microsoft.com' 
        appId = 'df67dcdb-c37d-46af-88e1-8b97951ca1c2'
        utterance = 'turn on all lights'
    
        headers = {
        }
    
        params ={
            'query': utterance,
            'timezoneOffset': '0',
            'verbose': 'true',
            'show-all-intents': 'true',
            'spellCheck': 'false',
            'staging': 'false',
            'subscription-key': key
        }
    
        r = requests.get(f'https://{endpoint}/luis/prediction/v3.0/apps/{appId}/slots/production/predict',headers=headers, params=params)
        print(r.json())
    
    except Exception as e:
        print(f'{e}')
    ```

1. Vervang de volgende waarden:

    * `YOUR-KEY` met uw start sleutel
    * `YOUR-ENDPOINT` met uw eind punt, bijvoorbeeld `westus2.api.cognitive.microsoft.com`

1. Afhankelijkheden installeren met de volgende console opdracht:

    ```console
    pip install requests
    ```

1. Voer het script uit met de volgende console opdracht:

    ```console
    python predict.py
    ``` 

1. Voorspellings antwoord in JSON-indeling controleren:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Het JSON-antwoord dat is opgemaakt voor de Lees baarheid: 

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="luis-keys"></a>LUIS-sleutels

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Quick Start, verwijdert u het bestand uit het bestands systeem. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uitingen en Train toevoegen](../get-started-get-model-rest-apis.md)