---
title: "Quickstart: Intentie ophalen met REST API's - LUIS"
titleSuffix: Azure Cognitive Services
description: In deze snelstartgids voor REST API gebruikt u een beschikbare openbare LUIS-app om de intentie van een gebruiker te bepalen aan de hand van beschrijvende tekst.
services: cognitive-services
author: nitinme
manager: diberry
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/19/2020
ms.author: nitinme
ms.openlocfilehash: 714d76641dd22f566b2464e3470f6a3324f4d758
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207184"
---
# <a name="quickstart-use-curl-and-rest-to-get-intent"></a>Quickstart: cURL en REST gebruiken om intent op te halen

Deze snelstart laat zien hoe u een LUIS-app kunt gebruiken om de intentie van een gebruiker te bepalen aan de hand van beschrijvende tekst. U gaat cURL gebruiken om de intentie van de gebruiker als tekst naar het HTTP-voorspellingseindpunt van de Pizza-app te verzenden. Bij het eindpunt wordt via LUIS het model van de Pizza-app toegepast om de betekenis van tekst in natuurlijke taal te analyseren. Hiermee wordt de algehele intentie bepaald en worden gegevens geëxtraheerd die relevant zijn voor het onderwerpdomein van de app.

[Referentiedocumentatie](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)

## <a name="prerequisites"></a>Vereisten

* Een gratis [LUIS](https://www.luis.ai)-account.
* Een teksteditor zoals [Visual Studio Code](https://code.visualstudio.com/).
* Het opdrachtregelprogramma cURL. Het programma cURL is al geïnstalleerd op macOS, de meeste Linux-distributies en Windows 10 build 1803 en hoger. Als u een cURL moet installeren, kunt u dit downloaden op de [Downloadpagina voor cURL](https://curl.haxx.se/download.html).

## <a name="create-pizza-app"></a>Pizza-app maken

[!INCLUDE [Create pizza app](includes/get-started-get-model-create-pizza-app.md)]

## <a name="get-intent-from-the-prediction-endpoint"></a>Intent ophalen vanaf het voorspellingseindpunt

Gebruik cURL om een query uit te voeren op het [voorspellingseindpunt](https://aka.ms/luis-apim-v3-prediction) en een voorspellingsresultaat op te halen.

1. Kopieer deze opdracht naar uw teksteditor:

    #### <a name="windows"></a>[Windows](#tab/windows)
    ```cURL
    curl "https://***YOUR-PREDICTION-ENDPOINT***/luis/prediction/v3.0/apps/***YOUR-APP-ID***/slots/production/predict" ^
          --request GET ^
          --get ^
          --data "subscription-key=***YOUR-PREDICTION-KEY***" ^
          --data "verbose=true" ^
          --data "show-all-intents=true" ^
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="macos"></a>[MacOS](#tab/unix)

    ```cURL
    curl "https://***YOUR-PREDICTION-ENDPOINT***/luis/prediction/v3.0/apps/***YOUR-APP-ID***/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=***YOUR-PREDICTION-KEY***" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "https://***YOUR-PREDICTION-ENDPOINT***/luis/prediction/v3.0/apps/***YOUR-APP-ID***/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=***YOUR-PREDICTION-KEY***" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    ***

1. Vervang de waarden die beginnen met `***YOUR-` door uw eigen waarden.

    |Informatie|Doel|
    |--|--|
    |`***YOUR-PREDICTION-ENDPOINT***`| Het eindpunt voor de voorspellings-URL. De Azure Resources-pagina voor uw app in het LUIS-portal.<br>Bijvoorbeeld `https://westus.api.cognitive.microsoft.com/`.|
    |`***YOUR-APP-ID***`|Uw app-ID. De pagina met toepassingsinstellingen voor uw app in het LUIS-portal.
    |`***YOUR-PREDICTION-KEY***`|Uw voorspellingssleutel van 32 tekens. De Azure Resources-pagina voor uw app in het LUIS-portal.

1. Kopieer de tekst naar een consolevenster en druk op Enter om de opdracht uit te voeren:

1. Voer het script uit met deze console-opdracht:

    ```console
    python predict.py
    ```

1. Controleer het voorspellingsantwoord dat geretourneerd wordt als JSON:

    ```console
    {"query":"I want two large pepperoni pizzas on thin crust please","prediction":{"topIntent":"ModifyOrder","intents":{"ModifyOrder":{"score":1.0},"None":{"score":8.55E-09},"Greetings":{"score":1.82222226E-09},"CancelOrder":{"score":1.47272727E-09},"Confirmation":{"score":9.8125E-10}},"entities":{"Order":[{"FullPizzaWithModifiers":[{"PizzaType":["pepperoni pizzas"],"Size":[["Large"]],"Quantity":[2],"Crust":[["Thin"]],"$instance":{"PizzaType":[{"type":"PizzaType","text":"pepperoni pizzas","startIndex":17,"length":16,"score":0.9978157,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Size":[{"type":"SizeList","text":"large","startIndex":11,"length":5,"score":0.9984481,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Quantity":[{"type":"builtin.number","text":"two","startIndex":7,"length":3,"score":0.999770939,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Crust":[{"type":"CrustList","text":"thin crust","startIndex":37,"length":10,"score":0.933985531,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"$instance":{"FullPizzaWithModifiers":[{"type":"FullPizzaWithModifiers","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.90681237,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"ToppingList":[["Pepperoni"]],"$instance":{"Order":[{"type":"Order","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.9047088,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"ToppingList":[{"type":"ToppingList","text":"pepperoni","startIndex":17,"length":9,"modelTypeId":5,"modelType":"List Entity Extractor","recognitionSources":["model"]}]}}}}
    ```

    JSON-antwoord opgemaakt voor leesbaarheid:

    ```JSON
    {
      "query": "I want two large pepperoni pizzas on thin crust please",
      "prediction": {
        "topIntent": "ModifyOrder",
        "intents": {
          "ModifyOrder": {
            "score": 1.0
          },
          "None": {
            "score": 8.55e-9
          },
          "Greetings": {
            "score": 1.82222226e-9
          },
          "CancelOrder": {
            "score": 1.47272727e-9
          },
          "Confirmation": {
            "score": 9.8125e-10
          }
        },
        "entities": {
          "Order": [
            {
              "FullPizzaWithModifiers": [
                {
                  "PizzaType": [
                    "pepperoni pizzas"
                  ],
                  "Size": [
                    [
                      "Large"
                    ]
                  ],
                  "Quantity": [
                    2
                  ],
                  "Crust": [
                    [
                      "Thin"
                    ]
                  ],
                  "$instance": {
                    "PizzaType": [
                      {
                        "type": "PizzaType",
                        "text": "pepperoni pizzas",
                        "startIndex": 17,
                        "length": 16,
                        "score": 0.9978157,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Size": [
                      {
                        "type": "SizeList",
                        "text": "large",
                        "startIndex": 11,
                        "length": 5,
                        "score": 0.9984481,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Quantity": [
                      {
                        "type": "builtin.number",
                        "text": "two",
                        "startIndex": 7,
                        "length": 3,
                        "score": 0.999770939,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Crust": [
                      {
                        "type": "CrustList",
                        "text": "thin crust",
                        "startIndex": 37,
                        "length": 10,
                        "score": 0.933985531,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ]
                  }
                }
              ],
              "$instance": {
                "FullPizzaWithModifiers": [
                  {
                    "type": "FullPizzaWithModifiers",
                    "text": "two large pepperoni pizzas on thin crust",
                    "startIndex": 7,
                    "length": 40,
                    "score": 0.90681237,
                    "modelTypeId": 1,
                    "modelType": "Entity Extractor",
                    "recognitionSources": [
                      "model"
                    ]
                  }
                ]
              }
            }
          ],
          "ToppingList": [
            [
              "Pepperoni"
            ]
          ],
          "$instance": {
            "Order": [
              {
                "type": "Order",
                "text": "two large pepperoni pizzas on thin crust",
                "startIndex": 7,
                "length": 40,
                "score": 0.9047088,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ],
            "ToppingList": [
              {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 17,
                "length": 9,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
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

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Utterances toevoegen en trainen](luis-get-started-rest-get-model.md)
