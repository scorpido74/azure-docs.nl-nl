---
title: "Quick Start: profiteren van intenties met REST-Api's-LUIS"
titleSuffix: Azure Cognitive Services
description: Gebruik in deze REST API Snelstartgids een beschik bare open bare LUIS-app om de bedoeling van een gebruiker te bepalen op basis van de conversatie tekst.
services: cognitive-services
author: roy-har
manager: diberry
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/19/2020
ms.author: v-royha
ms.openlocfilehash: 8248d441add1af39ab9d2d08a6dba85055f254b6
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119581"
---
# <a name="quickstart-use-curl-and-rest-to-get-intent"></a>Snelstartgids: gebruik krul en REST om het doel te verkrijgen

In deze Quick start ziet u hoe u een LUIS-app kunt gebruiken om te bepalen wat de bedoeling van een gebruiker is. U gaat krul gebruiken om de voor waarde van de gebruiker als tekst naar het HTTP prediction-eind punt van de pizza-app te verzenden. Op het eind punt past LUIS het model van de pizza-app toe om de tekst in de natuurlijke taal te analyseren, waardoor de algemene intentie kan worden bepaald en gegevens worden geëxtraheerd die relevant zijn voor het onderwerps domein van de app.

[Referentie documentatie](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)

## <a name="prerequisites"></a>Vereisten

* Een gratis [Luis](https://www.luis.ai) -account.
* Een tekst editor, zoals [Visual Studio code](https://code.visualstudio.com/).
* Het opdracht regel programma krul. Het krul programma is al geïnstalleerd op macOS, de meeste Linux-distributies en Windows 10 build 1803 en hoger. Als u een krul moet installeren, kunt u via de pagina voor het [krul](https://curl.haxx.se/download.html)een krul downloaden.

## <a name="create-pizza-app"></a>Pizza-app maken

[!INCLUDE [Create pizza app](includes/get-started-get-model-create-pizza-app.md)]

## <a name="get-intent-from-the-prediction-endpoint"></a>Intentie ophalen uit het Voorspellings eindpunt

Gebruik krul om het [Voorspellings eindpunt](https://aka.ms/luis-apim-v3-prediction) te doorzoeken en een Voorspellings resultaat te krijgen.

1. Kopieer deze opdracht naar uw tekst editor:

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

1. Vervang de waarden die beginnen met `***YOUR-` met uw eigen waarden.

    |Informatie|Functie|
    |--|--|
    |`***YOUR-PREDICTION-ENDPOINT***`| Het eind punt voor de voor Spellings-URL. Bevindt zich op de Azure-resources-pagina van de LUIS-portal voor uw app.<br>Bijvoorbeeld `https://westus.api.cognitive.microsoft.com/`.|
    |`***YOUR-APP-ID***`|Uw app-ID. Bevindt zich op de pagina LUIS-Portal, toepassings instellingen voor uw app.
    |`***YOUR-PREDICTION-KEY***`|Uw 32-teken Voorspellings sleutel. Bevindt zich op de Azure-resources-pagina van de LUIS-portal voor uw app.

1. Kopieer de tekst naar een console venster en druk op ENTER om de opdracht uit te voeren:

1. Voer het script uit met deze console opdracht:

    ```console
    python predict.py
    ```

1. Bekijk het Voorspellings antwoord dat wordt geretourneerd als JSON:

    ```console
    {"query":"I want two large pepperoni pizzas on thin crust please","prediction":{"topIntent":"ModifyOrder","intents":{"ModifyOrder":{"score":1.0},"None":{"score":8.55E-09},"Greetings":{"score":1.82222226E-09},"CancelOrder":{"score":1.47272727E-09},"Confirmation":{"score":9.8125E-10}},"entities":{"Order":[{"FullPizzaWithModifiers":[{"PizzaType":["pepperoni pizzas"],"Size":[["Large"]],"Quantity":[2],"Crust":[["Thin"]],"$instance":{"PizzaType":[{"type":"PizzaType","text":"pepperoni pizzas","startIndex":17,"length":16,"score":0.9978157,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Size":[{"type":"SizeList","text":"large","startIndex":11,"length":5,"score":0.9984481,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Quantity":[{"type":"builtin.number","text":"two","startIndex":7,"length":3,"score":0.999770939,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Crust":[{"type":"CrustList","text":"thin crust","startIndex":37,"length":10,"score":0.933985531,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"$instance":{"FullPizzaWithModifiers":[{"type":"FullPizzaWithModifiers","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.90681237,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"ToppingList":[["Pepperoni"]],"$instance":{"Order":[{"type":"Order","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.9047088,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"ToppingList":[{"type":"ToppingList","text":"pepperoni","startIndex":17,"length":9,"modelTypeId":5,"modelType":"List Entity Extractor","recognitionSources":["model"]}]}}}}
    ```

    JSON-antwoord dat is opgemaakt voor de Lees baarheid:

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
> [Uitingen en Train toevoegen](luis-get-started-rest-get-model.md)
