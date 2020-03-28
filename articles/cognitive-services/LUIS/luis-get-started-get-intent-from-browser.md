---
title: 'Snelstart: Maak intentie met browser - LUIS'
titleSuffix: Azure Cognitive Services
description: Gebruik in deze quickstart een beschikbare openbare LUIS-app om de intentie van een gebruiker te bepalen aan de uitspraaktekst in een browser.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: e06bb4c09b3ebab25c0c0ef8ac5c51f6842f34cd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987951"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Snelstart: Intent krijgen met een browser

Om te begrijpen wat een LUIS-voorspellingseindpunt retourneert, kunt u het resultaat van een voorspelling in een webbrowser bekijken.

## <a name="prerequisites"></a>Vereisten

Als u een openbare app wilt opvragen, hebt u het sein nodig:

* Uw eigen taalverstaan (LUIS) Authoring of Prediction-toets die kan worden verkregen via [LUIS Portal (Preview).](https://preview.luis.ai/) Als je nog geen abonnement hebt om een sleutel aan te maken, kun je je registreren voor een [gratis account.](https://azure.microsoft.com/free/)
* De id van de `df67dcdb-c37d-46af-88e1-8b97951ca1c2`openbare app: .

## <a name="use-the-browser-to-see-predictions"></a>De browser gebruiken om voorspellingen te zien

1. Open een webbrowser.
1. Gebruik de onderstaande volledige `YOUR-KEY` URL's, vervangen door uw eigen LUIS-authoring- of voorspellingssleutel. De aanvragen zijn GET-aanvragen en omvatten de autorisatie, met uw LUIS-ontwerp- of voorspellingssleutel, als parameter querytekenreeks.

    #### <a name="v3-prediction-request"></a>[V3 voorspellingsverzoek](#tab/V3-1-1)


    De indeling van de V3-URL voor een **GET-eindpunt** (per sleuven)-aanvraag is:

    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-request"></a>[V2 voorspellingsverzoek](#tab/V2-1-2)

    De indeling van de V2-URL voor een **GET-eindpuntaanvraag** is:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Plak de URL in een browservenster en druk op Enter. In de browser wordt een JSON-resultaat weergegeven dat aangeeft dat LUIS de intent `HomeAutomation.TurnOn` als de belangrijkste intent en de entiteit `HomeAutomation.Operation` met de waarde `on` detecteert.

    #### <a name="v3-prediction-response"></a>[V3 voorspellingsrespons](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[V2 voorspellingsrespons](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Als u alle intenties wilt zien, voegt u de juiste parameter querytekenreeks toe.

    #### <a name="v3-prediction-endpoint"></a>[V3 voorspelling eindpunt](#tab/V3-3-1)

    Toevoegen `show-all-intents=true` aan het einde van de querytekenreeks om **alle intenties weer**te geven:

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
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
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[V2 voorspellingeindpunt](#tab/V2)

    Toevoegen `verbose=true` aan het einde van de querytekenreeks om **alle intenties weer**te geven:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Een app maken in de LUIS-portal](get-started-portal-build-app.md)
