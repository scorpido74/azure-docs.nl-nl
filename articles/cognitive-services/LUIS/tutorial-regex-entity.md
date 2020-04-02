---
title: 'Zelfstudie: Entiteit met reguliere expressies - LUIS'
description: Extraheren consistent opgemaakte gegevens uit een utterance met behulp van de entiteit Reguliere expressie.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 5b585ee52880c474d3f2736b34a267012b390aad
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545838"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Zelfstudie: Goed opgemaakte gegevens uit de utterance halen
Maak in deze zelfstudie een entiteit met regelmatige expressies om consistent opgemaakte gegevens uit een utterance te extraheren.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App importeren
> * Intentie toevoegen
> * Een entiteit in de vorm van een reguliere expressie toevoegen
> * Trainen, publiceren en query-app om geëxtraheerde gegevens te krijgen

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entiteiten in de vorm van reguliere expressies

Gebruik de entiteit met reguliere expressie om goed opgemaakte tekst uit een utterance te halen. Hoewel de intentie van een uiting altijd wordt bepaald aan de hand van machine learning, wordt voor dit specifieke type entiteit geen machine learning gebruikt. Een goed gebruik voor de entiteit met reguliere expressie is elke tekst die consistent kan worden weergegeven door een [reguliere expressie](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

In dit voorbeeld wordt een _korte code_ gebruikt voor het verzenden van tekstberichten. Deze korte code is een 5 of 6-cijferige numerieke code, vooraf bevestigd `x\d{5,6}`met een x, en kan worden beschreven met de reguliere expressie .

Wanneer u een entiteit met reguliere expressie toevoegt aan een LUIS-app, hoeft u de tekst niet te [labelen](label-entity-example-utterance.md) met de reguliere expresentiteit. Het wordt toegepast op alle uitingen in alle opzichten.

## <a name="import-example-json-to-begin-app"></a>Voorbeeld .json importeren om app te starten

1.  Download en sla het [JSON-bestand van](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)de app op.

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Intentie maken voor het verzenden van bevestigingstekstberichten

1. Selecteer **+ Maak een** nieuwe intentie om de intentie van een utterance voor het verzenden van een bevestigingstekst te classificeren.

1. Voer in het pop-updialoogvenster `ConfirmationText` in en selecteer vervolgens **Done**.

1. Voeg voorbeelden van utterances toe aan de intentie.

    |Voorbeelden van utterances|
    |--|
    |Pizza levertijd naar x123432 verzenden|
    |Txt x234567 voor tijd|
    |x23987 voor de aankondiging|

    Als u door machines geleerde entiteiten wilt extraheren, moet u voorbeelden geven die de entiteit in verschillende uitingen opnemen, maar met deze niet-machine-geleerde entiteit is de variatie niet belangrijk. Zolang tekst overeenkomt met de reguliere expressie, wordt deze geëxtraheerd.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>De entiteit in de vorm van een gewone expressie gebruiken voor correct opgemaakte gegevens
Maak een entiteit met een reguliere expressie die overeenkomt met het tekstnummer. Deze reguliere expressie komt overeen met tekst, maar negeert case- en cultuurvarianten.

1. Selecteer **Entities** in het linkerpaneel.

1. Selecteer **+ Maak op** de lijstpagina Entiteiten.

1. Voer in het pop-updialoogvenster de `ConfirmationTextRegEx`nieuwe entiteitsnaam in, selecteer **RegEx** als entiteitstype en selecteer **Volgende**.

    > [!div class="mx-imgBorder"]
    > ![Stappen voor het maken van entiteiten voor reguliere expressies starten](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. Voer op **de entiteit Een regex maken**in `x\d{5,6}` als de **Regex-waarde** en selecteer **Vervolgens Maken**.

    > [!div class="mx-imgBorder"]
    > ![Reguliere expressie invoeren om gegevens uit voorbeeldutterance te extraheren](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Selecteer **Intents** in het linkermenu en vervolgens de intentie **ConfirmationText** om de reguliere expressie te zien die in de uitingen is gelabeld.

    > [!div class="mx-imgBorder"]
    > ![Reguliere expressie weergeven die is gelabeld in voorbeelduitingen](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Omdat de entiteit geen door de machine geleerde entiteit is, wordt de entiteit toegepast op de uitingen en weergegeven in de LUIS-portal zodra deze is gemaakt.

## <a name="train-the-app-before-testing-or-publishing"></a>De app trainen voordat u gaat testen of publiceren

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>De app publiceren om query's uit te voeren vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in de adresbalk en vervang _YOUR_QUERY_HERE_ door:

    `Text my pizza delivery to x23456 x234567 x12345`

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
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

    Met behulp van een entiteit in de vorm van een gewone expressie kan LUIS benoemde gegevens extraheren, wat vanuit programmatisch oogpunt nuttiger is voor de clienttoepassing dan het ontvangen van het JSON-antwoord.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Concept - entiteiten](luis-concept-entity-types.md)
* [JSON-referentie van entiteit voor reguliere expressie](reference-entity-regular-expression.md?tabs=V3)
* [Entiteiten toevoegen om gegevens te extraheren](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een nieuwe intentie gemaakt, voorbeelduitingen toegevoegd en een entiteit in de vorm van een reguliere expressie gemaakt om goed opgemaakte gegevens te extraheren uit de uitingen. Na het trainen en het publiceren van de app is met een query naar het eindpunt de intentie achterhaald. Vervolgens zijn de geëxtraheerde gegevens geretourneerd.

> [!div class="nextstepaction"]
> [Meer informatie over de entiteit lijst](tutorial-list-entity.md)

