---
title: 'Zelf studie: publicatie-instellingen-LUIS'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie wijzigt u de publicatie-instellingen voor het verbeteren van voor spellingen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: diberry
ms.openlocfilehash: 2df32c20bebf4243f383a0cccd8f037721533602
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890391"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Zelf studie: sentiment-analyse toevoegen als publicatie-instelling

In deze zelf studie wijzigt u de publicatie-instellingen om sentiment analyse te extra heren en vervolgens voert u een query uit op het LUIS-eind punt om de geretourneerde sentiment van een gebruiker utterance te bekijken.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Sentiment-analyse als een publicatie-instelling toevoegen
> * Sentiment van een utterance ophalen uit het gepubliceerde eind punt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Sentimentanalyse is een publicatie-instelling

De volgende utterances zijn voorbeelden van gevoelens:

|Sentiment|Score|Utterance|
|:--|:--|:--|
|negatief|0,01 |De pizza is awful.|
|positief|0,97 |De kaas pizza was geweldig.|

Sentimentanalyse is een publicatie-instelling die voor elke uiting geldt. Als uw app eenmaal is ingesteld, wordt de sentiment van een utterance geretourneerd zonder dat u gegevens hoeft te labelen.

Omdat het een publicatie-instelling is, ziet u deze niet op de pagina's doel items of entiteiten. De instelling is te zien in het deelvenster [Interactieve test](luis-interactive-test.md#view-sentiment-results) of bij het uitvoeren van tests bij de eindpunt-URL.

## <a name="import-example-json-to-begin-app"></a>Voor beeld van import. json voor het starten van de app

1.  Down load en sla het [JSON-bestand](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)van de app op.

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>De app trainen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>App configureren voor gebruik van sentimentanalyse

1. Selecteer **publiceren** in het bovenste menu. Sentiment analyse is een publicatie-instelling.

1. Selecteer **productie sleuf** en selecteer vervolgens **instellingen wijzigen**.
1. Stel de Sentimentanalyse-instelling in **op aan**.

    ![Sentimentanalyse als publicatie-instelling inschakelen](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Het gevoel van een uiting ophalen van het eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in het adres en voer de volgende utterance in:

    `Deliver 2 of the best cheese pizzas ever!!!`

    De laatste parameter van de queryreeks is `query`, de utterance **query**. Deze utterance is niet hetzelfde als een van de gelabelde utterances en dit is dus een goede test die de intentie `OrderPizza` als resultaat moet geven met de sentimentanalyse geëxtraheerd.

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    De sentiment-analyse is positief met een Score van 86%.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* Sentimentanalyse wordt geleverd door de Cognitive Service [Text Analytics](../Text-Analytics/index.yml). De functie is beperkt tot de door Text Analytics [ondersteunde talen](luis-language-support.md#languages-supported).
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie voegt u Sentimentanalyse toe als publicatie-instelling om de gevoelswaarden uit gehele uitingen op te halen.

> [!div class="nextstepaction"]
> [Eindpuntuitingen beoordelen in de HR-app](luis-tutorial-review-endpoint-utterances.md)

