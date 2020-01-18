---
title: 'Quick Start: gebruik browser-LUIS'
titleSuffix: Azure Cognitive Services
description: In deze Snelstartgids gebruikt u een beschik bare open bare LUIS-app om de bedoeling van een gebruiker te bepalen op basis van de conversatie tekst in een browser.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: 2ca0f9faf1623df9212072abbc960cba41a6414b
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165750"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Quick Start: gebruik een browser voor de bedoeling

Om te begrijpen wat een LUIS-voorspellingseindpunt retourneert, kunt u het resultaat van een voorspelling in een webbrowser bekijken. 

## <a name="prerequisites"></a>Vereisten

Als u een query wilt uitvoeren op een open bare app, hebt u het volgende nodig:

* Uw eigen Language Understanding (LUIS) ontwerpen of Voorspellings sleutel die kan worden verkregen via de [Luis-Portal (preview)](https://preview.luis.ai/). Als u nog geen abonnement hebt om een sleutel te maken, kunt u zich registreren voor een [gratis account](https://azure.microsoft.com/free/). 
* De ID van de open bare app: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>De browser gebruiken om voor spellingen te bekijken

1. Open een webbrowser. 
1. Gebruik de volledige Url's hieronder om `YOUR-KEY` te vervangen door uw eigen LUIS-ontwerp of Voorspellings sleutel. De aanvragen zijn GET-aanvragen en bevatten de autorisatie, met uw LUIS-ontwerp of Voorspellings sleutel, als een query reeks parameter.

    #### <a name="v3-prediction-requesttabv3-1-1"></a>[V3-Voorspellings aanvraag](#tab/V3-1-1)
    
    
    De indeling van de V3-URL voor een aanvraag voor het **ophalen** van een eind punt (per sleuf) is:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-requesttabv2-1-2"></a>[V2-Voorspellings aanvraag](#tab/V2-1-2)
    
    De indeling van de v2-URL voor een aanvraag voor het **ophalen** van een eind punt is:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Plak de URL in een browservenster en druk op Enter. In de browser wordt een JSON-resultaat weergegeven dat aangeeft dat LUIS de intent `HomeAutomation.TurnOn` als de belangrijkste intent en de entiteit `HomeAutomation.Operation` met de waarde `on` detecteert.

    #### <a name="v3-prediction-responsetabv3-2-1"></a>[Voorspellings antwoord voor v3](#tab/V3-2-1)

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

    #### <a name="v2-prediction-responsetabv2-2-2"></a>[V2-Voorspellings reactie](#tab/V2-2-2)

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

    #### <a name="v3-prediction-endpointtabv3-3-1"></a>[V3-Voorspellings eindpunt](#tab/V3-3-1)

    Voeg `show-all-intents=true` toe aan het einde van de query reeks om **alle intenties weer te geven**:

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

    #### <a name="v2-prediction-endpointtabv2"></a>[V2-Voorspellings eindpunt](#tab/V2)

    Voeg `verbose=true` toe aan het einde van de query reeks om **alle intenties weer te geven**:

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

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Een app maken in de LUIS-Portal](get-started-portal-build-app.md)
