---
title: 'Zelf studie: reguliere expressie entiteit-LUIS'
description: Extraheer consistente, opgemaakte gegevens van een utterance met behulp van de reguliere expressie-entiteit.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 5b585ee52880c474d3f2736b34a267012b390aad
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80545838"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Zelf studie: goed opgemaakte gegevens ophalen uit de utterance
In deze zelf studie maakt u een reguliere expressie-entiteit om consistent opgemaakte gegevens te halen uit een utterance.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App importeren
> * Intentie toevoegen
> * Een entiteit in de vorm van een reguliere expressie toevoegen
> * App trainen, publiceren en doorzoeken om geëxtraheerde gegevens op te halen

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entiteiten in de vorm van reguliere expressies

Gebruik de entiteit reguliere expressie om goed opgemaakte tekst uit een utterance op te halen. Hoewel de intentie van een uiting altijd wordt bepaald aan de hand van machine learning, wordt voor dit specifieke type entiteit geen machine learning gebruikt. Een goed gebruik voor de entiteit reguliere expressie is tekst die consistent kan worden weer gegeven met een [reguliere expressie](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

In dit voor beeld wordt een _korte code_ gebruikt voor het verzenden van tekst berichten. Deze korte code is een numerieke code van vijf of 6 cijfers, voorafgegaan door een x en kan worden beschreven met de reguliere expressie `x\d{5,6}`.

Wanneer u een reguliere expressie-entiteit toevoegt aan een LUIS-app, hoeft u geen [Label](label-entity-example-utterance.md) te plaatsen voor de tekst met de reguliere Express-entiteit. Deze wordt toegepast op alle uitingen in alle intenties.

## <a name="import-example-json-to-begin-app"></a>Voor beeld van import. json voor het starten van de app

1.  Down load en sla het [JSON-bestand](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)van de app op.

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Intentie maken voor het verzenden van bevestigings tekst berichten

1. Selecteer **+ maken** om een nieuwe intentie te maken voor het classificeren van de intentie van een utterance voor het verzenden van een bevestigings tekst.

1. Voer in het pop-updialoogvenster `ConfirmationText` in en selecteer vervolgens **Done**.

1. Voeg voorbeelden van utterances toe aan de intentie.

    |Voorbeelden van utterances|
    |--|
    |Pizza-leverings tijd verzenden naar x123432|
    |Txt x234567 voor tijd|
    |x23987 voor de kennisgeving|

    Als u door machines geleerde entiteiten wilt extra heren, geeft u voor beelden op die de entiteit bevatten in verschillende uitingen, maar met deze niet-door machines geleerde entiteit, is de variatie niet belang rijk. Zolang de tekst overeenkomt met de reguliere expressie, wordt deze geëxtraheerd.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>De entiteit in de vorm van een gewone expressie gebruiken voor correct opgemaakte gegevens
Maak een reguliere expressie-entiteit die overeenkomt met het tekst nummer. Deze reguliere expressie komt overeen met tekst, maar negeert hoofdletter-en cultuur varianten.

1. Selecteer **Entities** in het linkerpaneel.

1. Selecteer **+ maken** op de lijst pagina met entiteiten.

1. Voer in het pop-updialoogvenster de naam `ConfirmationTextRegEx`van de nieuwe entiteit in, selecteer **regex** als entiteits type en selecteer vervolgens **volgende**.

    > [!div class="mx-imgBorder"]
    > ![Beginnen met het maken van entiteits stappen voor een reguliere expressie-entiteit](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. Voer `x\d{5,6}` in de **entiteit Create a regex**de waarde voor **regex** in, en selecteer vervolgens **maken**.

    > [!div class="mx-imgBorder"]
    > ![Voer een reguliere expressie in voor het extra heren van gegevens uit het voor beeld utterance](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Selecteer **intenties** in het linkermenu en klik vervolgens op de **ConfirmationText** voor een overzicht van de reguliere expressie in de uitingen.

    > [!div class="mx-imgBorder"]
    > ![Reguliere expressie weer geven die is gelabeld in voor beeld uitingen](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Omdat de entiteit geen door de machine geleerde entiteit is, wordt de entiteit toegepast op de uitingen en weer gegeven in de LUIS-Portal zodra deze wordt gemaakt.

## <a name="train-the-app-before-testing-or-publishing"></a>De app trainen voordat u gaat testen of publiceren

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>De app publiceren om query's uit te voeren vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in de adres balk en vervang _YOUR_QUERY_HERE_ door:

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

* [Concept-entiteiten](luis-concept-entity-types.md)
* [JSON-verwijzing van de entiteit van de reguliere expressie](reference-entity-regular-expression.md?tabs=V3)
* [Entiteiten toevoegen om gegevens te extra heren](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een nieuwe intentie gemaakt, voorbeelduitingen toegevoegd en een entiteit in de vorm van een reguliere expressie gemaakt om goed opgemaakte gegevens te extraheren uit de uitingen. Na het trainen en het publiceren van de app is met een query naar het eindpunt de intentie achterhaald. Vervolgens zijn de geëxtraheerde gegevens geretourneerd.

> [!div class="nextstepaction"]
> [Meer informatie over de entiteit lijst](tutorial-list-entity.md)

