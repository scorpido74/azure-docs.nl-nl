---
title: 'Quickstart: Intentie met browser-LUIS'
titleSuffix: Azure Cognitive Services
description: In deze Snelstartgids gebruikt u een beschik bare open bare LUIS-app om de bedoeling van een gebruiker te bepalen op basis van de conversatie tekst in een browser.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703132"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Quickstart: Intentie verkrijgen met een browser

Om te begrijpen wat een LUIS-voorspellingseindpunt retourneert, kunt u het resultaat van een voorspelling in een webbrowser bekijken. 

## <a name="prerequisites"></a>Vereisten

Als u een query wilt uitvoeren op een open bare app, hebt u het volgende nodig:

* Uw eigen Language Understanding-sleutel (LUIS). Als u nog geen abonnement hebt om een sleutel te maken, kunt u zich registreren voor een [gratis account](https://azure.microsoft.com/free/).
* De ID van de open bare app: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>De browser gebruiken om voor spellingen te bekijken

1. Open een webbrowser. 
1. Gebruik de volledige Url's hieronder om `{your-key}` te vervangen door uw eigen LUIS-sleutel. De aanvragen zijn GET-aanvragen en bevatten de autorisatie, met uw LUIS-sleutel, als een query reeks parameter.

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2-aanvraag voor Voorspellings eindpunt](#tab/V2)
    
    De indeling van de v2-URL voor een aanvraag voor het **ophalen** van een eind punt is:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3-Voorspellings eindpunt aanvraag](#tab/V3)
    
    
    De indeling van de V3-URL voor een aanvraag voor het **ophalen** van een eind punt (per sleuf) is:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. Plak de URL in een browservenster en druk op Enter. In de browser wordt een JSON-resultaat weergegeven dat aangeeft dat LUIS de intent `HomeAutomation.TurnOn` als de belangrijkste intent en de entiteit `HomeAutomation.Operation` met de waarde `on` detecteert.

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

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

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
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

    * * *

1. Als u alle intenties wilt zien, voegt u de juiste query teken reeks parameter toe. 

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

    #### <a name="v3-prediction-endpointtabv3"></a>[V3-Voorspellings eindpunt](#tab/V3)

    Voeg `show-all-intents=true` toe aan het einde van de query reeks om **alle intenties weer te geven**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
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

    * * * 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Een app maken in de LUIS-Portal](get-started-portal-build-app.md)