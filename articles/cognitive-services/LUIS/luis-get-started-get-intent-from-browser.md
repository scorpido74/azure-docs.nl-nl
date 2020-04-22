---
title: 'Snelstart: Query voor voorspelling met browser - LUIS'
description: Gebruik in deze quickstart een beschikbare openbare LUIS-app om de intentie van een gebruiker te bepalen aan de uitspraaktekst in een browser.
ms.topic: quickstart
ms.date: 04/21/2020
ms.openlocfilehash: 5ba86882ebf3cb538ad6b865382342fcbd43d27c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769973"
---
# <a name="quickstart-query-prediction-runtime-with-user-text"></a>Snelstart: runtime van queryvoorspelling met gebruikerstekst

Om te begrijpen wat een LUIS-voorspellingseindpunt retourneert, kunt u het resultaat van een voorspelling in een webbrowser bekijken.

## <a name="prerequisites"></a>Vereisten

Als u een openbare app wilt opvragen, hebt u het sein nodig:

* Brongegevens van uw taalbegrip (LUIS):
    * **Voorspelling som -** die kan worden verkregen bij [LUIS Portal](https://www.luis.ai/). Als je nog geen abonnement hebt om een sleutel aan te maken, kun je je registreren voor een [gratis account.](https://azure.microsoft.com/free/)
    * **Voorbeeldsubdomein voorspellen** - het subdomein is ook de **naam** van uw LUIS-bron.
* Een LUIS-app-id - gebruik de `df67dcdb-c37d-46af-88e1-8b97951ca1c2`openbare IoT-app-id van . De gebruikersquery die wordt gebruikt in de quickstartcode is specifiek voor die app.

## <a name="use-the-browser-to-see-predictions"></a>De browser gebruiken om voorspellingen te zien

1. Open een webbrowser.
1. Gebruik de onderstaande volledige `YOUR-KEY` URL's, vervangen door uw eigen LUIS Prediction-toets. De aanvragen zijn GET-aanvragen en omvatten de autorisatie, met uw LUIS-voorspellingssleutel, als parameter querytekenreeks.

    #### <a name="v3-prediction-request"></a>[V3 voorspellingsverzoek](#tab/V3-1-1)


    De indeling van de V3-URL voor een **GET-eindpunt** (per sleuven)-aanvraag is:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[V2 voorspellingsverzoek](#tab/V2-1-2)

    De indeling van de V2-URL voor een **GET-eindpuntaanvraag** is:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
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
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
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
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
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

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:
* [V3 voorspelling eindpunt](luis-migration-api-v3.md)
* [Aangepaste subdomeinen](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Een app maken in de LUIS-portal](get-started-portal-build-app.md)
