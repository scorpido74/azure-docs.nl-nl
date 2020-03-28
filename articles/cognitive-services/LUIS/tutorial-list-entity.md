---
title: 'Zelfstudie: Entiteit lijst - LUIS'
description: Leer hoe u gegevens ophaalt die overeenkomen met een vooraf gedefinieerde lijst met items. Elk item in de lijst kan synoniemen hebben die ook exact overeenkomen
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 1cfeccbd54e8ef8ec315d53fc7a766760c92a0d1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79297404"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Zelfstudie: exacte gegevens met tekstovereenkomende gegevens ophalen uit een utterance met lijstentiteit

In deze zelfstudie u begrijpen hoe u gegevens krijgen die precies overeenkomen met een vooraf gedefinieerde lijst met items.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App importeren en bestaande intenties gebruiken
> * Een entiteit List toevoegen
> * Trainen, publiceren en query-app om geëxtraheerde gegevens te krijgen

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Wat is een lijstentiteit?

Een lijstentiteit is een exacte tekstovereenkomst met de woorden in de utterance. Elk item in de lijst kan een lijst met synoniemen bevatten. Gebruik een lijstentiteit wanneer u een exacte overeenkomst wilt.

Maak voor deze geïmporteerde pizzatoepassing een lijstentiteit voor de verschillende soorten pizzakorst.

Een entiteit List is een goede keuze voor dit type gegevens wanneer:

* De gegevenswaarden deel uitmaken van een bekende set.
* De set maximale [begrenzingen](luis-boundaries.md) van LUIS voor dit entiteitstype niet overschrijdt.
* De tekst in de utterance is een case-ongevoelige overeenkomst met een synoniem of de canonieke naam. LUIS gebruikt de lijst niet verder dan de wedstrijd. Stammen, meervoudsvormen en andere varianten kunnen niet worden verwerkt met alleen een lijstentiteit. Hiervoor kunt u overwegen een [patroon](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) met de optionele tekstsyntaxis te gebruiken.

> [!CAUTION]
> Als u niet zeker weet of u een lijstentiteit of een door machines geleerde entiteit met een woordgroepals beschrijving wilt, is de beste en meest flexibele praktijk het gebruik van een door de machine geleerde entiteit met een woordgroeplijst als beschrijving. Met deze methode kan LUIS de waarden van de gegevens leren en uitbreiden om te extraheren.

## <a name="import-example-json-and-add-utterances"></a>Voorbeeld .json importeren en uitingen toevoegen

1.  Download en sla het [JSON-bestand van](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)de app op.

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. De geïmporteerde app `OrderPizza` heeft een intentie. Selecteer die intentie en voeg een paar uitingen toe met nieuwe korsttypen:

    |Nieuwe uitingen|
    |--|--|
    |bestel een pan korst kleine pepperoni pizza|
    |3 dunne korst hawaiian pizza's|
    |leveren 2 gevulde korst pizza's met brood stokken|
    |een dikke korst pizza voor pick-up|
    |een diepe schotel pepperoni pizza|

## <a name="crust-list-entity"></a>De lijstentiteit van de korst

Nu de **orderpizza-intentie** voorbeelduitingen heeft met korsttypen, moet LUIS begrijpen welke woorden de korsttypen vertegenwoordigen.

Voorbeelden van de primaire naam en synoniemen zijn:

|Canonieke naam|Synoniemen|
|--|--|
|Diep gerecht|Diep<br>diepe schotelkorst<br>Dikke<br>dikke korst|
|Pan|Regelmatige<br>Origineel<br>Normale<br>gewone korst<br>originele korst<br>normale korst|
|Gevuld|gevulde korst|
|Dun|dunne korst<br>Mager<br>magere korst|

1. Selecteer **Entities** in het linkerpaneel.

1. Selecteer **+ Maken**.

1. Voer in het pop-updialoogvenster `CrustList` in als naam voor de entiteit en **List** als het entiteitstype. Selecteer **Volgende**.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van het pop-updialoogvenster voor het maken van een nieuwe entiteit](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. Voer op de pagina **Een lijstentiteit maken** de canonieke namen en synoniemen voor elke canonieke naam in en selecteer **Maken**.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het toevoegen van items aan lijstentiteit](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    Wanneer u een lijstentiteit toevoegt aan een LUIS-app, hoeft u de tekst niet te [labelen](label-entity-example-utterance.md) met de lijstentiteit. Het wordt toegepast op alle uitingen in alle opzichten.

## <a name="train-the-app-before-testing-or-publishing"></a>De app trainen voordat u gaat testen of publiceren

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>De app publiceren om query's uit te voeren vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Ga naar het einde van de URL in het adres en voer de volgende utterance in:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    De laatste querytekenreeksparameter is `query`de **utterancequery**.


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
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
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
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

    De soorten korst werden gevonden als exacte tekst komt overeen en keerde terug in de JSON-reactie. Deze informatie wordt door de clienttoepassing gebruikt om de bestelling te verwerken.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Entiteit List](luis-concept-entity-types.md#list-entity) - conceptuele informatie
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)
* [Concept - entiteiten](luis-concept-entity-types.md)
* [JSON-referentie van entiteit voor reguliere expressie](reference-entity-regular-expression.md?tabs=V3)
* [Entiteiten toevoegen om gegevens te extraheren](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Volgende stappen
Deze zelfstudie heeft voorbeelduitingen toegevoegd en vervolgens een lijstentiteit gemaakt om exacte tekstovereenkomsten uit de uitingen te extraheren. Na het trainen en het publiceren van de app is met een query naar het eindpunt de intentie achterhaald. Vervolgens zijn de geëxtraheerde gegevens geretourneerd.

> [!div class="nextstepaction"]
> [Vooraf gebouwde entiteit toevoegen met een rol](tutorial-entity-roles.md)

