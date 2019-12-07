---
title: 'Zelf studie: eind punt uitingen-LUIS controleren'
titleSuffix: Azure Cognitive Services
description: Verbeter de voorspellingen van de app door de uitingen die worden ontvangen via het LUIS-HTTP-eindpunt (en waar LUIS niet zeker over is) te controleren of corrigeren. Bij sommige uitingen moet mogelijk de intentie worden gecontroleerd en bij andere de entiteit.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 0a4d2a3345ce4f69d4492d1a782b778b1ee3bf4c
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895679"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Zelf studie: herstel onzekere voor spellingen door eind punt uitingen te controleren
In deze zelf studie kunt u app-voor spellingen verbeteren door uitingen te verifiëren of corrigeren, ontvangen via het HTTPS-eind punt van LUIS, dat LUIS niet zeker is van. U moet eind punt uitingen bekijken als een standaard onderdeel van uw geplande LUIS onderhoud.

Dit controle proces stelt LUIS in staat om uw app-domein te leren. LUIS selecteert de uitingen die in de controle lijst worden weer gegeven. Deze lijst:

* is specifiek voor deze app;
* is bedoeld om de nauwkeurigheid van de app te verbeteren;
* dient op periodieke basis te worden gecontroleerd.

Door de eindpuntuitingen te boordelen, kunt u de voorspelde intenties controleren of corrigeren.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

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

1.  Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json) en sla het op.

1. Importeer het JSON-bestand in een nieuwe app in de LUIS-portal van de [Preview-versie](https://preview.luis-ai).

1. Ga naar het gedeelte **Beheren**, open het tabblad **Versies**, kloon de versie en noem deze `review`.

    > [!TIP]
    > Het klonen van een nieuwe versie is een best practice voordat u uw app wijzigt. Wanneer u klaar bent met het volt ooien van een versie, exporteert u de versie (als een. json-of. lu-bestand) en controleert u het bestand in uw bron beheersysteem.


1. Als u de app wilt trainen, selecteert u **trainen**.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>De app publiceren voor toegang tot het HTTP-eind punt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Uitingen toevoegen aan het eind punt

In deze app hebt u intenties en entiteiten, maar u hebt geen eindpunt gebruik. Dit eindpunt gebruik is vereist om de app te verbeteren met de controle van het eind punt utterance.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gebruik het eindpunt om de volgende uitingen toe te voegen.

    |Eind punt utterance|Uitgelijnde intentie|
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

Controleer de uitingen van het eind punt voor een juiste, afgestemde intentie. Hoewel er één groep uitingen is om te controleren in alle versies, voegt het proces van het correct uitlijnen van het voor beeld utterance toe aan het huidige _actieve model_ .

1. Ga naar de sectie **Build** van de portal en selecteer **eind punt uitingen** in het navigatie venster aan de linkerkant. De lijst is gefilterd voor de intentie **ApplyForJob**.

    > [!div class="mx-imgBorder"]
    > ![scherm opname van de knop eind punt uitingen in de linkernavigatiebalk bekijken](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Deze utterance, `I'm looking for a job with Natural Language Processing`, bevindt zich niet in de juiste intentie.

1.  Als u deze utterance wilt uitlijnen, selecteert u op de rij utterance de juiste **uitgelijnde intentie** van `GetJobInformation`. Voeg het gewijzigde utterance toe aan de app door het selectie vakje in te scha kelen.

    > [!div class="mx-imgBorder"]
    > ![scherm opname van de knop eind punt uitingen in de linkernavigatiebalk bekijken](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Bekijk de resterende uitingen in dit voor behoud en corrigeer zo nodig de uitgelijnde intentie. Gebruik de eerste utterance-tabel in deze zelf studie om de uitgelijnde intentie weer te geven.

    De uitingen-lijst van het **controle-eind punt** mag niet langer de gecorrigeerde uitingen hebben. Als er meer uitingen worden weer gegeven, gaat u door met de lijst en corrigeert u uitgelijnde intenten totdat de lijst leeg is.

    Een correctie van entiteit labeling wordt uitgevoerd nadat de intentie is uitgelijnd, op de pagina Details van het doel.

1. De app opnieuw trainen en publiceren.

## <a name="get-intent-prediction-from-endpoint"></a>Intentie van het eind punt ophalen

Als u wilt controleren of het correct uitgelijnde voor beeld uitingen de voor spelling van de app heeft verbeterd, probeert u een utterance te sluiten op de gecorrigeerde utterance.

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Ga naar het einde van de URL in het adres en voer `Are there any natural language processing jobs in my department right now?` in. De laatste parameter van de queryreeks is `q`, de utterance **query**.

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

   Nu de onzekere uitingen goed zijn afgestemd, is de juiste opzet gedicteerd met een **hoge score**.

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
