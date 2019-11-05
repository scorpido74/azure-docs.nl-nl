---
title: 'Zelf studie: vooraf ontwikkelde intenties en entiteiten-LUIS'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie voegt u vooraf gemaakte intenties en entiteiten toe aan een app om snel intenties te kunnen voorspellen en gegevens te extraheren. U hoeft geen utterances met vooraf gedefinieerde entiteiten te labelen. De entiteit wordt automatisch gedetecteerd.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: diberry
ms.openlocfilehash: 0a59d9783eac122f96b1671f2dba5d0d708e1d83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499401"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Zelf studie: algemene intenties en entiteiten identificeren

In deze zelfstudie voegt u vooraf gemaakte intenties en entiteiten toe aan de zelfstudie-app Human Resources om snel intenties te kunnen voorspellen en gegevens te extraheren. U hoeft geen utterances met vooraf gedefinieerde entiteiten te markeren omdat de entiteit automatisch wordt gedetecteerd.

Vooraf gemaakte modellen (domeinen, intenties en entiteiten) helpen u om het model snel te bouwen.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Nieuwe app maken
> * Vooraf gemaakte intenties toevoegen 
> * Vooraf gemaakte entiteiten toevoegen 
> * Trainen 
> * Publiceren 
> * Intenties en entiteiten ophalen van eindpunt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Een nieuwe app maken

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Vooraf gemaakte intenties toevoegen om u te helpen met algemene gebruikersintenties

LUIS biedt verschillende vooraf gemaakte intenties om u te helpen met algemene gebruikersintenties.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecteer **Add prebuilt domain intent**. 

1. Zoeken naar `Utilities`. 

1. Selecteer alle doel stellingen en selecteer **gereed**. Deze intenties zijn handig om te bepalen waar de gebruiker zich bevindt in de conversatie en wat ze willen doen. 

## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Vooraf gemaakte entiteiten toevoegen om te helpen met het ophalen van algemene gegevens

LUIS biedt verschillende vooraf gemaakte entiteiten voor het ophalen van algemene gegevens. 

1. Selecteer **Entities** in het navigatiemenu aan de linkerkant.

1. Selecteer de knop **Add prebuilt entity**.

1. Selecteer de volgende entiteiten in de lijst met vooraf gedefinieerde entiteiten en selecteer **Gereed**:

   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     Met deze entiteit kunt u de herkenning van de locatie toevoegen aan uw client toepassing.

## <a name="add-example-utterances-to-the-none-intent"></a>Voorbeelduitingen toevoegen aan de intentie None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>De app trainen zodat de wijzigingen aan de intentie kunnen worden getest 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>De app publiceren zodat op het getrainde model query's kunnen worden uitgevoerd vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Ga naar het einde van de URL in de adresbalk van de browser en voer `I want to cancel my trip to Seattle` in. De laatste parameter van de queryreeks is `q`, de utterance**query**. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.1055009
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.1055009
        },
        {
          "intent": "Utilities.SelectItem",
          "score": 0.02659072
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0253379084
        },
        {
          "intent": "Utilities.ReadAloud",
          "score": 0.02528683
        },
        {
          "intent": "Utilities.SelectNone",
          "score": 0.02434013
        },
        {
          "intent": "Utilities.Escalate",
          "score": 0.009161292
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006861785
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00633448
        },
        {
          "intent": "Utilities.ShowNext",
          "score": 0.0053827134
        },
        {
          "intent": "None",
          "score": 0.002602003
        },
        {
          "intent": "Utilities.ShowPrevious",
          "score": 0.001797354
        },
        {
          "intent": "Utilities.SelectAny",
          "score": 0.000831930141
        },
        {
          "intent": "Utilities.Repeat",
          "score": 0.0006924066
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.000606057351
        },
        {
          "intent": "Utilities.GoBack",
          "score": 0.000276725681
        },
        {
          "intent": "Utilities.FinishTask",
          "score": 0.000267822179
        },
        {
          "intent": "Utilities.Reject",
          "score": 3.21784828E-05
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        }
      ]
    }
    ```

    Het resultaat dat de Hulpprogram Ma's heeft voor speld. Annuleer intentie met een betrouw baarheid van 80% en de plaatsings gegevens worden geëxtraheerd. 


## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

Meer informatie over vooraf gemaakte modellen:

* [Vooraf gemaakte domeinen](luis-reference-prebuilt-domains.md): dit zijn algemene domeinen die het algehele ontwerp van LUIS-apps versnellen
* Vooraf gedefinieerde intenties: dit zijn de afzonderlijke intenties van de algemene domeinen. U kunt intenties afzonderlijk toevoegen in plaats van het hele domein toe te voegen.
* [Vooraf gemaakte entiteiten](luis-prebuilt-entities.md): dit zijn veelvoorkomende gegevenstypen die nuttig zijn voor de meeste LUIS-apps.

Meer informatie over het werken met uw LUIS-app:

* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)

## <a name="next-steps"></a>Volgende stappen

Door vooraf gemaakte intents en entiteiten toe te voegen, kan de clienttoepassing algemene gebruikersintents bepalen en algemene gegevenstypen extraheren.  

> [!div class="nextstepaction"]
> [Een entiteit in de vorm van een reguliere expressie aan de app toevoegen](luis-quickstart-intents-regex-entity.md)

