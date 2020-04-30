---
title: Doel intentie met REST-aanroep in python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 2307ef6ea8940a3b3ddfb8c7539f4f809dc4c52c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733299"
---
## <a name="prerequisites"></a>Vereisten

* [Python 3,6](https://www.python.org/downloads/) of hoger.
* [Visual Studio Code](https://code.visualstudio.com/)
* Een LUIS-App-ID: gebruik de open bare IoT `df67dcdb-c37d-46af-88e1-8b97951ca1c2`-app-id van. De gebruikers query die in de Quick Start code wordt gebruikt, is specifiek voor die app.

## <a name="create-luis-runtime-key-for-predictions"></a>Een LUIS-runtime sleutel maken voor voor spellingen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)
1. Klik [op **Language Understanding** maken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Voer alle vereiste instellingen voor de **runtime** sleutel in:

    |Instelling|Waarde|
    |--|--|
    |Naam|Gewenste naam (2-64 tekens)|
    |Abonnement|Selecteer het juiste abonnement|
    |Locatie|Selecteer een locatie in de buurt en beschik bare locaties|
    |Prijscategorie|`F0`-de minimale prijs categorie|
    |Resourcegroep|Een beschik bare resource groep selecteren|

1. Klik op **maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, gaat u naar de pagina resource.
1. Verzameling geconfigureerd `endpoint` en een `key`.

## <a name="get-intent-from-the-prediction-endpoint"></a>Intentie ophalen uit het Voorspellings eindpunt

Gebruik python om het [Voorspellings eindpunt](https://aka.ms/luis-apim-v3-prediction) te doorzoeken en een Voorspellings resultaat te krijgen.

1. Kopieer dit code fragment naar een bestand met `predict.py`de naam:

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

1. Vervang de `YOUR-KEY` waarden `YOUR-ENDPOINT` en door uw eigen Voorspellings **runtime** sleutel en eind punt.

    |Informatie|Doel|
    |--|--|
    |`YOUR-KEY`|De **runtime** -sleutel voor de voor spelling van 32-tekens.|
    |`YOUR-ENDPOINT`| Het eind punt voor de voor Spellings-URL. Bijvoorbeeld `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. De `requests` afhankelijkheid installeren. Dit wordt gebruikt om HTTP-aanvragen te maken:

    ```console
    pip install requests
    ```

1. Voer het script uit met deze console opdracht:

    ```console
    python predict.py
    ```

1. Bekijk het Voorspellings antwoord dat wordt geretourneerd als JSON:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Dit is het JSON-antwoord dat is opgemaakt voor de Lees baarheid:

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

Wanneer u klaar bent met deze Quick Start, verwijdert u het bestand uit het bestands systeem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uitingen en Train toevoegen](../get-started-get-model-rest-apis.md)