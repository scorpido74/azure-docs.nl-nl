---
title: Intent krijgen met REST-oproep in Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 23ac98f91c989c9bedb6b91e6a7ce26dc164ac5a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987760"
---
## <a name="prerequisites"></a>Vereisten

* [Python 3.6](https://www.python.org/downloads/) of hoger.
* [Visual Studio-code](https://code.visualstudio.com/)
* Openbare app-id:`df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>Luis-runtime-sleutel maken voor voorspellingen

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Klik [op **Taalverstaan maken** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Voer alle vereiste instellingen voor **Runtime-toets** in:

    |Instelling|Waarde|
    |--|--|
    |Name|Gewenste naam (2-64 tekens)|
    |Abonnement|Passend abonnement selecteren|
    |Locatie|Selecteer een nabijgelegen en beschikbare locatie|
    |Prijscategorie|`F0`- de minimale prijscategorie|
    |Resourcegroep|Een beschikbare resourcegroep selecteren|

1. Klik **op Maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, navigeert u naar de resourcepagina.
1. Verzamel geconfigureerd `endpoint` en `key`een .

## <a name="get-intent-from-the-prediction-endpoint"></a>Intentie ophalen van het voorspellingseindpunt

Gebruik Python om het [voorspellingseindpunt](https://aka.ms/luis-apim-v3-prediction) op te vragen en een voorspellingsresultaat te krijgen.

1. Kopieer dit codefragment naar `predict.py`een bestand met de naam:

    ```python
    ########### Python 3.6 #############
    import requests

    try:

        key = 'YOUR-KEY' # your Runtime key
        endpoint = 'YOUR-ENDPOINT' # such as 'your-resource-name.api.cognitive.microsoft.com'
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

1. Vervang `YOUR-KEY` de `YOUR-ENDPOINT` waarden en waarden door uw eigen voorspelling **Runtime-toets** en eindpunt.

    |Informatie|Doel|
    |--|--|
    |`YOUR-KEY`|Uw 32-tekenvoorspelling **Runtime-toets.**|
    |`YOUR-ENDPOINT`| Uw voorspelling URL eindpunt. Bijvoorbeeld `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Installeer `requests` de afhankelijkheid. Dit wordt gebruikt om HTTP-verzoeken te doen:

    ```console
    pip install requests
    ```

1. Voer uw script uit met deze opdracht console:

    ```console
    python predict.py
    ```

1. Bekijk de voorspellingsrespons, die wordt geretourneerd als JSON:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Hier is de JSON reactie opgemaakt voor leesbaarheid:

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

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze quickstart, verwijdert u het bestand uit het bestandssysteem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uitingen toevoegen en trainen](../get-started-get-model-rest-apis.md)