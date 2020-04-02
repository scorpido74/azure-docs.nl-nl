---
title: 'Zelfstudie: Publicatie-instellingen - LUIS'
description: Wijzig in deze zelfstudie de publicatie-instellingen om voorspellingen te verbeteren.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 19913d16ecb1457ad4edb93ea34e4b96a590aca0
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545776"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Zelfstudie: Sentimentanalyse toevoegen als publicatie-instelling

Wijzig in deze zelfstudie de publicatie-instellingen om sentimentanalyse te extraheren en vervolgens het LUIS-eindpunt op te vragen om het geretourneerde sentiment van een gebruikersutterance te zien.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Sentimentanalyse toevoegen als publicatie-instelling
> * Het sentiment van een utterance ophalen van het gepubliceerde eindpunt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Sentimentanalyse is een publicatie-instelling

De volgende utterances zijn voorbeelden van gevoelens:

|Sentiment|Score|Utterance|
|:--|:--|:--|
|negatief|0,01 |De pizza was verschrikkelijk.|
|positief|0.97 |De kaas pizza was geweldig.|

Sentimentanalyse is een publicatie-instelling die voor elke uiting geldt. Eenmaal ingesteld, retourneert uw app het gevoel van een utterance zonder dat u gegevens hoeft te labelen.

Omdat het een publicatie-instelling is, ziet u deze niet gelabeld op de intenties of entiteitenpagina's. De instelling is te zien in het deelvenster [Interactieve test](luis-interactive-test.md#view-sentiment-results) of bij het uitvoeren van tests bij de eindpunt-URL.

## <a name="import-example-json-to-begin-app"></a>Voorbeeld .json importeren om app te starten

1.  Download en sla het [JSON-bestand van](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)de app op.

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>De app trainen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>App configureren voor gebruik van sentimentanalyse

1. Selecteer **Publiceren** in het bovenste menu. Sentimentanalyse is een publicatie-instelling.

1. Selecteer **Productiesleuf** en selecteer **Instellingen wijzigen**.
1. Stel de instelling Gevoelanalyse in **op Aan**.

    ![Sentimentanalyse als publicatie-instelling inschakelen](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Het gevoel van een uiting ophalen van het eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in de adresbalk en vervang _YOUR_QUERY_HERE_ door:

    `Deliver 2 of the best cheese pizzas ever!!!`

    Deze utterance is niet hetzelfde als een van de gelabelde utterances en dit is dus een goede test die de intentie `OrderPizza` als resultaat moet geven met de sentimentanalyse geëxtraheerd.

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

    De sentimentanalyse is positief met een score van 86%.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* Sentimentanalyse wordt geleverd door de Cognitive Service [Text Analytics](../Text-Analytics/index.yml). De functie is beperkt tot de door Text Analytics [ondersteunde talen](luis-language-support.md#languages-supported).
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie voegt u Sentimentanalyse toe als publicatie-instelling om de gevoelswaarden uit gehele uitingen op te halen.

> [!div class="nextstepaction"]
> [Eindpuntuitingen beoordelen in de HR-app](luis-tutorial-review-endpoint-utterances.md)

