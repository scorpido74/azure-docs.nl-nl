---
title: 'Zelfstudie: Reguliere expressie-entiteit-LUIS'
titleSuffix: Azure Cognitive Services
description: U kunt consistent opgemaakte gegevens uit een uiting extraheren met de reguliere-expressie-entiteit.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: db60d87b441836f38309166318dd1778ce828483
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307588"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Zelfstudie: Correct opgemaakte gegevens ophalen uit utterances
In deze zelfstudie maakt u een app voor het extraheren van consistent opgemaakte gegevens uit een utterance met behulp van de entiteit **Regular Expression (regex)** .

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Een nieuwe app maken 
> * Intentie toevoegen
> * Een entiteit in de vorm van een reguliere expressie toevoegen 
> * Trainen
> * Publiceren
> * Intenties en entiteiten ophalen van eindpunt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entiteiten in de vorm van reguliere expressies

De app maakt gebruik van een entiteit in de vorm van een reguliere expressie om correct opgemaakte nummers van HR-formulieren (Human Resources) op te halen uit een utterance. Hoewel de intentie van een uiting altijd wordt bepaald aan de hand van machine learning, wordt voor dit specifieke type entiteit geen machine learning gebruikt. 

**Enkele voorbeelden van uitingen:**

|Voorbeelden van utterances|
|--|
|Waar is HRF-123456?|
|Wie is de auteur van HRF-123234?|
|Is HRF-456098 gepubliceerd in het Frans?|
|HRF-456098|
|Wat is de datum van HRF-456098?|
 
Een reguliere expressie is een goede keuze voor dit type gegevens wanneer:

* de gegevens goed zijn opgemaakt.


## <a name="create-a-new-app"></a>Een nieuwe app maken

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Intentie voor het vinden van formulier maken

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecteer **Create new intent**. 

1. Voer in het pop-updialoogvenster `FindForm` in en selecteer vervolgens **Done**. 

    ![Schermopname van dialoogvenster voor het maken van een nieuwe intentie met Utilities in het zoekvak](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. Voeg voorbeelden van utterances toe aan de intentie.

    |Voorbeelden van utterances|
    |--|
    |What is the URL for hrf-123456?|
    |Where is hrf-345678?|
    |When was hrf-456098 updated?|
    |Did John Smith update hrf-234639 last week?|
    |Hoeveel versies van hrf-345123 zijn er?|
    |Who needs to authorize form hrf-123456?|
    |How many people need to sign off on hrf-345678?|
    |hrf-234123 date?|
    |author of hrf-546234?|
    |title of hrf-456234?|

    [![Schermopname van de pagina Intent met de nieuwe utterances gemarkeerd](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>De entiteit in de vorm van een gewone expressie gebruiken voor correct opgemaakte gegevens
De entiteit in de vorm van een gewone expressie die moet overeenkomen met het formuliernummer is `hrf-[0-9]{6}`. Deze gewone expressie zoekt overeenkomsten voor de letterlijke tekens `hrf-`, maar negeert varianten in hoofdlettergebruik en cultuur. De cijfers 0-9 moeten tot 6 cijfers exact overeenkomen.

HRF staat voor `human resources form`.

LUIS tokeniseert de uiting wanneer deze wordt toegevoegd aan een intentie. Dit houdt in dat er in deze utterances spaties worden toegevoegd vóór en na het afbreekstreepje, `Where is HRF - 123456?`. De gewone expressie wordt toegepast op de utterance in de onbewerkte vorm, dus voordat deze is getokeniseerd. Omdat de expressie wordt toegepast op de _onbewerkte_ vorm, hoeft er geen rekening te worden gehouden met woordgrenzen. 

Voer de volgende stappen uit om een entiteit in de vorm van een gewone expressie te maken om aan LUIS door te geven wat de indeling van een HRF-nummer is:

1. Selecteer **Entities** in het linkerpaneel.

1. Selecteer **Create new entity** op de pagina Entities. 

1. Geef in het pop-updialoogvenster de naam `HRF-number` op voor de nieuwe entiteit, selecteer **RegEx** als het type entiteit, voer `hrf-[0-9]{6}` in als **RegEx**-waarde en selecteer ten slotte **Gereed**.

    ![Schermopname van pop-updialoogvenster met eigenschappen voor nieuwe entiteit](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. Selecteer **Intenties** in het menu links en selecteer vervolgens de intentie **FindForm** om de gewone expressie te zien die is gelabeld in de uitingen. 

    [![Schermopname van het labelen van een utterance met een bestaande entiteit en regex-patroon](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Omdat de entiteit niet wordt verkregen via machine learning, wordt deze toegepast op de utterances en weergegeven op de website van LUIS zodra de entiteit is gemaakt.

## <a name="add-example-utterances-to-the-none-intent"></a>Voorbeelduitingen toevoegen aan de intentie None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>De app trainen voordat u gaat testen of publiceren

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>De app publiceren om query's uit te voeren vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Ga naar het einde van de URL in het adres en voer de volgende utterance in:

    `When were HRF-123456 and hrf-234567 published in the last year?`

    De laatste parameter van de queryreeks is `q`, de utterance **query**. Deze utterance is niet hetzelfde als een van de gelabelde utterances en dit is dus een goede test die de intentie `FindForm` als resultaat moet geven met de twee formuliernummers `HRF-123456` en `hrf-234567`.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        }
      ]
    }
    ```

    Met behulp van een entiteit in de vorm van een gewone expressie kan LUIS benoemde gegevens extraheren, wat vanuit programmatisch oogpunt nuttiger is voor de clienttoepassing dan het ontvangen van het JSON-antwoord.


## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* Concepten van entiteit in de vorm van een [reguliere expressie](luis-concept-entity-types.md#regular-expression-entity)
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een nieuwe intentie gemaakt, voorbeelduitingen toegevoegd en een entiteit in de vorm van een reguliere expressie gemaakt om goed opgemaakte gegevens te extraheren uit de uitingen. Na het trainen en het publiceren van de app is met een query naar het eindpunt de intentie achterhaald. Vervolgens zijn de geëxtraheerde gegevens geretourneerd.

> [!div class="nextstepaction"]
> [Meer informatie over de entiteit lijst](luis-quickstart-intent-and-list-entity.md)

