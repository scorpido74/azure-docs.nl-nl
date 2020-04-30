---
title: 'Zelf studie: lijst entiteit-LUIS'
description: Leer hoe u gegevens ophaalt die overeenkomen met een vooraf gedefinieerde lijst met items. Elk item in de lijst kan synoniemen hebben die ook exact overeenkomen
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 9530719c43260751d64d7ccf446bc7941078d6e9
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101124"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Zelf studie: exacte tekst-overeenkomende gegevens ophalen uit een utterance met een lijst entiteit

In deze zelf studie leert u hoe u gegevens kunt ophalen die exact overeenkomen met een vooraf gedefinieerde lijst met items.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App importeren en bestaande intentie gebruiken
> * Een entiteit List toevoegen
> * App trainen, publiceren en doorzoeken om geëxtraheerde gegevens op te halen

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Wat is een lijstentiteit?

Een lijstentiteit is een exacte tekstovereenkomst met de woorden in de utterance. Elk item in de lijst kan een lijst met synoniemen bevatten. Gebruik een lijst entiteit als u een exacte overeenkomst wilt.

Maak voor deze geïmporteerde pizza-toepassing een lijst entiteit voor de verschillende soorten pizza-crust.

Een entiteit List is een goede keuze voor dit type gegevens wanneer:

* De gegevenswaarden deel uitmaken van een bekende set.
* De set maximale [begrenzingen](luis-limits.md) van LUIS voor dit entiteitstype niet overschrijdt.
* De tekst in de utterance is een hoofdletter gevoelige overeenkomst met een synoniem of canonieke naam. LUIS gebruikt de lijst niet verder dan de overeenkomst. Stammen, meervoudsvormen en andere varianten kunnen niet worden verwerkt met alleen een lijstentiteit. Hiervoor kunt u overwegen een [patroon](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) met de optionele tekstsyntaxis te gebruiken.

> [!CAUTION]
> Als u niet zeker weet of u een lijst entiteit of een door een machine geleerde entiteit met een woordgroepen lijst als descriptor wilt gebruiken, is de beste en meest flexibele manier om een door een machine geleerde entiteit met een woordgroepen lijst als descriptor te maken. Met deze methode kan LUIS de waarden van de gegevens die moeten worden geëxtraheerd, leren en uitbreiden.

## <a name="import-example-json-and-add-utterances"></a>Voor beeld van import. json en uitingen toevoegen

1.  Down load en sla het [JSON-bestand](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)van de app op.

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. De geïmporteerde app heeft een `OrderPizza` intentie. Selecteer die intentie en voeg enkele uitingen toe met nieuwe crust-typen:

    |Nieuwe uitingen|
    |--|--|
    |Bestel een pan crust Small pepperoni pizza|
    |3-smalle crust Hawaï pizzas|
    |lever twee gecrusteerde pizzas met brood sticks|
    |een dik crust pizza voor ophalen|
    |een diepe schaal-pepperoni pizza|

## <a name="crust-list-entity"></a>Entiteit van crust-lijst

Nu de **OrderPizza** -intentie bijvoorbeeld uitingen met crust-typen bevat, moet Luis begrijpen welke woorden de crust-typen vertegenwoordigen.

Voor beelden van de primaire naam en synoniemen zijn:

|Canonieke naam|Synoniemen|
|--|--|
|Diep gerecht|Deep<br>diepe schotel crust<br>Haas<br>dik crust|
|Pannen|standaard<br>Origineel<br>opmaakprofiel<br>normale crust<br>oorspronkelijke crust<br>normale crust|
|Knuffel|gevulde crust|
|Dun|dunne crust<br>dun<br>smal crust|

1. Selecteer **Entities** in het linkerpaneel.

1. Selecteer **+ maken**.

1. Voer in het pop-updialoogvenster `CrustList` in als naam voor de entiteit en **List** als het entiteitstype. Selecteer **Next**.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van het pop-updialoogvenster voor het maken van een nieuwe entiteit](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. Voer op de pagina **een lijst met entiteiten maken** de canonieke namen en synoniemen in voor elke canonieke naam en selecteer vervolgens **maken**.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding van het toevoegen van items aan de lijst entiteit](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    Wanneer u een lijst entiteit toevoegt aan een LUIS-app, hoeft u geen [Label](label-entity-example-utterance.md) te geven voor de tekst van de entiteit. Deze wordt toegepast op alle uitingen in alle intenties.

## <a name="train-the-app-before-testing-or-publishing"></a>De app trainen voordat u gaat testen of publiceren

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>De app publiceren om query's uit te voeren vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Ga naar het einde van de URL in het adres en voer de volgende utterance in:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    De laatste query string- `query`para meter is, de utterance **query's**.


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

    De typen crust zijn gevonden als exacte tekst overeenkomsten en geretourneerd in het JSON-antwoord. Deze informatie wordt gebruikt door de client toepassing om de order te verwerken.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Entiteit List](luis-concept-entity-types.md#list-entity) - conceptuele informatie
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)
* [Concept-entiteiten](luis-concept-entity-types.md)
* [JSON-verwijzing van de entiteit van de reguliere expressie](reference-entity-regular-expression.md?tabs=V3)
* [Entiteiten toevoegen om gegevens te extra heren](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie is voor beeld uitingen toegevoegd en vervolgens een lijst entiteit gemaakt voor het extra heren van exacte tekst overeenkomsten van de uitingen. Na het trainen en het publiceren van de app is met een query naar het eindpunt de intentie achterhaald. Vervolgens zijn de geëxtraheerde gegevens geretourneerd.

> [!div class="nextstepaction"]
> [Een vooraf samengestelde entiteit met een rol toevoegen](tutorial-entity-roles.md)

