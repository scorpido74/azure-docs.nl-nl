---
title: 'Zelfstudie: Eindpuntuitingen controleren _LUIS'
description: In deze zelfstudie leert u de voorspellingen van de app te verbeteren door de uitingen die worden ontvangen via het LUIS-HTTP-eindpunt (en waar LUIS niet zeker over is) te controleren of corrigeren. Bij sommige uitingen moet mogelijk de intentie worden gecontroleerd en bij andere de entiteit.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/02/2020
ms.openlocfilehash: b8f8fa2cd3c9c22187bb95c55d9de2abb2e8caec
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324634"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Zelfstudie: Onzekere voorspellingen herstellen door eindpuntuitingen te controleren
In deze zelfstudie leert u de voorspellingen van de app te verbeteren door de uitingen die worden ontvangen via het LUIS-HTTP-eindpunt (en waar LUIS niet zeker over is) te controleren of corrigeren. Controleer de eindpuntuitingen regelmatig als onderdeel van uw geplande LUIS-onderhoud.

Door dit beoordelingsproces kan LUIS meer over uw app-domein leren. LUIS selecteert de uitingen die in de beoordelingslijst worden weergegeven. Deze lijst:

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

## <a name="download-json-file-for-app"></a>JSON-bestand voor de app downloaden

Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true) en sla het op.

## <a name="import-json-file-for-app"></a>JSON-bestand voor de app importeren


1. Selecteer **+ Nieuwe app voor conversatie** en vervolgens **Importeren als JSON** in het [LUIS-portal](https://www.luis.ai) op de pagina **Mijn app**. Zoek het opgeslagen JSON-bestand uit de vorige stap. U hoeft de naam van de app niet te wijzigen. Selecteer **Gereed**.

1. Selecteer **Build** en vervolgens **Intenties** om de intenties te bekijken, de belangrijke bouwstenen van een LUIS-app.

    :::image type="content" source="media/luis-tutorial-review-endpoint-utterances/initial-intents-in-app.png" alt-text="Ga van de pagina Versies naar de pagina Intenties.":::

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>De app trainen om de entiteitswijzigingen toe te passen op de app

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>De app publiceren voor toegang vanaf het HTTP-eindpunt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Uitingen toevoegen aan het eindpunt

In deze app hebt u intenties en entiteiten, maar geen eindpuntgebruik. Dit eindpuntgebruik is vereist om de app te verbeteren met de controle van eindpuntuitingen.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in de adresbalk en vervang _YOUR_QUERY_HERE_ door de uitingen in de volgende tabel. Verzend de uiting en krijg het resultaat voor elke uiting. Vervang vervolgens de uiting aan het einde door de volgende uiting.

    |Eindpuntuiting|Uitgelijnde intentie|
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

Controleer de eindpuntuitingen voor correct uitgelijnde intenties. Hoewel er één groep uitingen is om in alle versies te controleren, voegt de correcte uitlijning van de intentie de voorbeeldintentie enkel toe aan het huidige _actieve model_.

1. Selecteer in het onderdeel **Build** van het portaal **Eindpuntuitingen controleren** in de linkernavigatiebalk. De lijst is gefilterd voor de intentie **ApplyForJob**.

    :::image type="content" source="./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png" alt-text="Ga van de pagina Versies naar de pagina Intenties.":::

    Deze uiting, `I'm looking for a job with Natural Language Processing`, is niet de correcte intentie, _GetJobInformation_. Deze is ten onrechte voorspeld als _ApplyForJob_ omwille van de overeenkomsten in de taaknaam en werkwoorden in de twee intenties.

1.  Als u deze uiting wilt uitlijnen, selecteert de correcte **Uitgelijnde intentie** van `GetJobInformation`. Voeg de gewijzigde uiting toe aan de app door het selectievakje in te schakelen.

    Bekijk de resterende uitingen in deze intentie en corrigeer waar nodig de uitgelijnde intentie. Gebruik de tabel met initiële uitingen in deze zelfstudie om de uitgelijnde intentie te bekijken.

    De lijst **Eindpuntuitingen controleren** zou de verbeterde uitingen niet meer mogen bevatten. Als meer uitingen worden weergegeven, doorloop dan verder de lijst en verbeter uitgelijnde intenties tot de lijst leeg is.

    Correcties van de labels van entiteiten gebeuren nadat de intentie is uitgelijnd, op de pagina met details voor de intentie.

1. De app opnieuw trainen en publiceren.

## <a name="get-intent-prediction-from-endpoint"></a>Intentvoorspelling ophalen vanaf eindpunt

Als u wilt controleren of de correct uitgelijnde voorbeelduitingen de voorspelling door de app hebben verbeterd, probeer dan een uiting die lijkt op de gecorrigeerde uiting.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in de adresbalk en vervang _YOUR_QUERY_HERE_ door `Are there any natural language processing jobs in my department right now?`.

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.901367366
                },
                "ApplyForJob": {
                    "score": 0.0307973567
                },
                "EmployeeFeedback": {
                    "score": 0.0296942145
                },
                "MoveEmployee": {
                    "score": 0.00739785144
                },
                "FindForm": {
                    "score": 0.00449316856
                },
                "Utilities.Stop": {
                    "score": 0.00417657848
                },
                "Utilities.StartOver": {
                    "score": 0.00407167152
                },
                "Utilities.Help": {
                    "score": 0.003662492
                },
                "None": {
                    "score": 0.00335733569
                },
                "Utilities.Cancel": {
                    "score": 0.002225436
                },
                "Utilities.Confirm": {
                    "score": 0.00107437756
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
                                        "value": "2020-07-02 21:45:50"
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

   Nu de onzekere uitingen correct zijn uitgelijnd, is de juiste intentie voorspeld met een **hoge score**.

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
