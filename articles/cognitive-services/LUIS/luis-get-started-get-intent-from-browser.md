---
title: 'Quick Start: een query uitvoeren voor de voor spelling met browser-LUIS'
description: In deze Snelstartgids gebruikt u een beschik bare open bare LUIS-app om de bedoeling van een gebruiker te bepalen op basis van de conversatie tekst in een browser.
ms.topic: quickstart
ms.date: 04/21/2020
ms.openlocfilehash: 5ba86882ebf3cb538ad6b865382342fcbd43d27c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81769973"
---
# <a name="quickstart-query-prediction-runtime-with-user-text"></a>Snelstartgids: Query's voor voor spellingen uitvoeren met gebruikers tekst

Om te begrijpen wat een LUIS-voorspellingseindpunt retourneert, kunt u het resultaat van een voorspelling in een webbrowser bekijken.

## <a name="prerequisites"></a>Vereisten

Als u een query wilt uitvoeren op een open bare app, hebt u het volgende nodig:

* De resource gegevens van uw Language Understanding (LUIS):
    * **Voorspellings sleutel** : deze kan worden verkregen via de [Luis-Portal](https://www.luis.ai/). Als u nog geen abonnement hebt om een sleutel te maken, kunt u zich registreren voor een [gratis account](https://azure.microsoft.com/free/).
    * **Subdomein voor Voorspellings eindpunt** -het subdomein is ook de **naam** van uw Luis-resource.
* Een LUIS-App-ID: gebruik de open bare IoT `df67dcdb-c37d-46af-88e1-8b97951ca1c2`-app-id van. De gebruikers query die in de Quick Start code wordt gebruikt, is specifiek voor die app.

## <a name="use-the-browser-to-see-predictions"></a>De browser gebruiken om voor spellingen te bekijken

1. Open een webbrowser.
1. Gebruik de volledige Url's hieronder en vervang `YOUR-KEY` deze door uw eigen Luis-Voorspellings sleutel. De aanvragen zijn GET-aanvragen en bevatten de autorisatie, met uw LUIS-Voorspellings sleutel, als een query reeks parameter.

    #### <a name="v3-prediction-request"></a>[V3-Voorspellings aanvraag](#tab/V3-1-1)


    De indeling van de V3-URL voor een aanvraag voor het **ophalen** van een eind punt (per sleuf) is:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[V2-Voorspellings aanvraag](#tab/V2-1-2)

    De indeling van de v2-URL voor een aanvraag voor het **ophalen** van een eind punt is:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. Plak de URL in een browservenster en druk op Enter. In de browser wordt een JSON-resultaat weergegeven dat aangeeft dat LUIS de intent `HomeAutomation.TurnOn` als de belangrijkste intent en de entiteit `HomeAutomation.Operation` met de waarde `on` detecteert.

    #### <a name="v3-prediction-response"></a>[Voorspellings antwoord voor v3](#tab/V3-2-1)

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

    #### <a name="v2-prediction-response"></a>[V2-Voorspellings reactie](#tab/V2-2-2)

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

1. Als u alle intenties wilt zien, voegt u de juiste query teken reeks parameter toe.

    #### <a name="v3-prediction-endpoint"></a>[V3-Voorspellings eindpunt](#tab/V3-3-1)

    Voeg `show-all-intents=true` aan het einde van de query reeks toe dat **alle intenties worden weer gegeven**:

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

    #### <a name="v2-prediction-endpoint"></a>[V2-Voorspellings eindpunt](#tab/V2)

    Voeg `verbose=true` aan het einde van de query reeks toe dat **alle intenties worden weer gegeven**:

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
* [V3-Voorspellings eindpunt](luis-migration-api-v3.md)
* [Aangepaste subdomeinen](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Een app maken in de LUIS-Portal](get-started-portal-build-app.md)
