---
title: 'Zelfstudie: Eindpuntuitingen controleren - LUIS'
description: Verbeter in deze zelfstudie app-voorspellingen door uitingen die zijn ontvangen via het LUIS HTTP-eindpunt waarvan LUIS niet zeker is, te verifiëren of te corrigeren. Bij sommige uitingen moet mogelijk de intentie worden gecontroleerd en bij andere de entiteit.
services: cognitive-services
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 307c18d3326cb1a64b884463a571985a015834ed
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548728"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Zelfstudie: Herstel van onzekere voorspellingen door eindpuntuitingen te bekijken
Verbeter in deze zelfstudie app-voorspellingen door uitingen te verifiëren of te corrigeren die zijn ontvangen via het LUIS HTTPS-eindpunt waarvan LUIS niet zeker is. U moet eindpuntuitingen controleren als een regelmatig onderdeel van uw geplande LUIS-onderhoud.

Met dit beoordelingsproces kan LUIS uw app-domein leren. LUIS selecteert de uitingen die in de beoordelingslijst worden weergegeven. Deze lijst:

* is specifiek voor deze app;
* is bedoeld om de nauwkeurigheid van de app te verbeteren;
* dient op periodieke basis te worden gecontroleerd.

Door de eindpuntuitingen te boordelen, kunt u de voorspelde intenties controleren of corrigeren.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Eindpuntuitingen controleren
> * App trainen en publiceren
> * Eindpunt van app opvragen om JSON-antwoord van LUIS te zien

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Voorbeeld-app importeren

Gebruik de volgende stappen om een app te importeren.

1.  Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true) en sla het op.

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>De app trainen om de entiteitswijzigingen toe te passen op de app

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>De app publiceren om deze te openen vanaf het HTTP-eindpunt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Uitingen toevoegen aan het eindpunt

In deze app hebt u intents en entiteiten, maar hebt u geen eindpuntgebruik. Dit eindpuntgebruik is vereist om de app te verbeteren met de utterancereview van eindpunten.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in de adresbalk en vervang _YOUR_QUERY_HERE_ door de uitingen in de volgende tabel. Voor elke utterance dient u de utterance in en krijgt u het resultaat. Vervang vervolgens de utterance aan het einde door de volgende utterance.

    |Eindpuntutterance|Uitgelijnde intentie|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    Er is één groep uitingen om te beoordelen, ongeacht welke versie u actief bewerkt of welke versie van de app wordt gepubliceerd op het eindpunt.

## <a name="review-endpoint-utterances"></a>Eindpuntuitingen controleren

Controleer de eindpuntuitingen op correct uitgelijnde intentie. Hoewel er één groep uitingen is die in alle versies moeten worden beoordeeld, wordt met het proces om de intentie correct uit te lijnen, alleen de voorbeeldutterance toegevoegd aan het huidige _actieve model._

1. Selecteer **Eindpuntuitingen controleren** via de linkernavigatie in het gedeelte **Bouwen** van de portal. De lijst is gefilterd voor de intentie **ApplyForJob**.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van de knop Eindpuntuitingen beoordelen in de navigatiebalk aan de linkerkant](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Deze uiting, `I'm looking for a job with Natural Language Processing`, is niet in de juiste bedoeling.

1.  Als u deze utterance wilt uitlijnen, selecteert u `GetJobInformation`op de utterancerij de juiste **uitgelijnde intentie** van . Voeg de gewijzigde utterance toe aan de app door het vinkje te selecteren.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van de knop Eindpuntuitingen beoordelen in de navigatiebalk aan de linkerkant](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Controleer de resterende uitingen in deze intentie en corrigeer de uitgelijnde intentie indien nodig. Gebruik de eerste utterancetabel in deze zelfstudie om de uitgelijnde intentie weer te geven.

    De lijst **Eindpuntuitingen controleren** mag niet langer de gecorrigeerde uitingen hebben. Als er meer uitingen worden weergegeven, blijft u door de lijst werken en uitgelijnde intenties corrigeren totdat de lijst leeg is.

    Elke correctie van entiteitsetikettering gebeurt nadat de intentie is uitgelijnd, vanaf de pagina Intentiedetails.

1. De app opnieuw trainen en publiceren.

## <a name="get-intent-prediction-from-endpoint"></a>Intent-voorspelling ophalen vanaf eindpunt

Als u wilt controleren of de correct uitgelijnde voorbeelduitingen de voorspelling van de app hebben verbeterd, probeert u een uiting in de buurt van de gecorrigeerde utterance.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL _YOUR_QUERY_HERE_ in `Are there any natural language processing jobs in my department right now?`de adresbalk en vervang YOUR_QUERY_HERE door .

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2019-12-05 23:23:53"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
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

   Nu de onzekere uitingen correct zijn uitgelijnd, werd de juiste intentie voorspeld met een **hoge score**.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Kan de beoordeling worden vervangen door meer uitingen toe te voegen?
U vraagt zich misschien af waarom we niet meer voorbeelduitingen toevoegen. Wat is het doel van de beoordeling van eindpuntuitingen? In een echte LUIS-app zijn de eindpuntuitingen van gebruikers met een woordkeuze en indeling die u nog niet hebt gebruikt. Als u de dezelfde woordkeuze en rangschikking had gebruikt, zou de oorspronkelijke voorspelling een hoger percentage hebben gehad.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Waarom staat de bovenste intentie in de lijst met uitingen?
Sommige eindpuntuitingen hebben een hoge voorspellingsscore in de beoordelingslijst. U dient deze uitingen alsnog te beoordelen en verifiëren. Ze zijn opgenomen in de lijst omdat de beoordeling van de tweede intentie een score had die te dicht lag bij de score van de hoogst scorende intentie. Er moet ongeveer 15% verschil zijn tussen de bovenste twee intenties.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uitingen bekeken die bij het eindpunt zijn ingediend en waar LUIS niet zeker van was. Zodra deze uitingen zijn gecontroleerd en als voorbeelduitingen naar de juiste intenties zijn verplaatst, kan LUIS de voorspellingsnauwkeurigheid vergroten.

> [!div class="nextstepaction"]
> [Meer informatie over het gebruik van patronen](luis-tutorial-pattern.md)
