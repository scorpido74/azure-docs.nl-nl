---
title: 'Tutorial: Predict intentions - LUIS'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie maakt u een aangepaste app waarmee de intentie van een gebruiker wordt voorspeld. Deze app is het eenvoudigste type LUIS-app omdat de app geen verschillende gegevenselementen uit de utterancetekst extraheert, zoals e-mailadressen of datums.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 4d096ee829a425af3763c212daf5049acccf9f19
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325939"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Tutorial: Build a LUIS app to determine user intentions

In this tutorial, you create a custom app that predicts a user's intention based on the utterance (text). 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Een nieuwe app maken 
> * Intents maken
> * Voorbeelden van utterances toevoegen
> * App trainen
> * App publiceren
> * Get intent prediction from endpoint


[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]

## <a name="user-intentions-as-intents"></a>Gebruikersintenties als intents

Het doel van de app is om de intentie van natuurlijke omgangstaal in een tekst te bepalen: 

`I'd like to order a veggie pizza with a salad on the side.`

Deze intenties worden onderverdeeld in **Intents**. 

|Intentie|Doel|
|--|--|
|`ModifyOrder`|Determine user's pizza order.|
|`Greeting`|Begin bot conversation.|
|`ConfirmOrder`|Confirm pizza order.|
|`None`|Determine if user is asking something the app is not supposed to answer. Deze intent is geleverd als onderdeel van het maken van de app en kan niet worden verwijderd. |

## <a name="create-a-new-app"></a>Een nieuwe app maken

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Een nieuwe intent maken 

1. In the portal, inside the app's **Build** section, select **+ Create**. Enter the new intent name, `OrderPizza`, then select **Done**.

    The `OrderPizza` intent is predicted when: a user wants to order a pizza. 

1. Voeg verschillende voorbeelden van utterances toe aan deze intent waarnaar een gebruiker waarschijnlijk zal vragen:

    |`OrderPizza` example utterances|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|

    ![Voorbeelden van utterances toevoegen](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Door _voorbeelden van utterances_ op te geven traint u LUIS in het soort utterances dat voor deze intent kan worden voorspeld. 

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]    

## <a name="create-remaining-intents"></a>Create remaining intents

1. Create the `Greeting` intent and add the following example utterances. This is the intent to determine if a user is beginning a new pizza order conversation.

    |`Greeting` example utterances|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Create the `Confirm` intent and add the following example utterances. This is the intent to determine if a user is done ordering and accepts the order details. 

    |`Confirm` example utterances|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>None intent example utterances

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>De app trainen 

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Publish the app 

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)] 

## <a name="get-intent-prediction"></a>Get intent prediction

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Go to the end of the URL in the address bar and enter:

    `get a medium vegetarian pizza for delivery` 

    This is not exactly the same as an example utterance so it is a good test to see if LUIS can learn what should be predicted with this intent.

    De laatste parameter van de queryreeks is `query`, de utterance**query**. Deze utterance is niet hetzelfde als een van de voorbeeldutterances. Dit is een goede test die de `OrderPizza`-intent als de best scorende intent moet retourneren. 

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    The entities array is empty because this app currently does not have any entities (unit of data inside the utterance to extract). 

    Het JSON-resultaat identificeert de hoogst scorende intent als **`prediction.topIntent`** -eigenschap. All scores are between 1 and 0, with the better score being closer to 1. 

1. Change the URL **query** parameter to target the **Greeting** intent:

    `Howdy`

    This is not exactly the same as an example utterance so it is a good test to see if LUIS can learn what should be predicted with this intent. 

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }    
    ```
 
    This prediction has a 44% confidence score. To increase the confidence score, add between 15 and 30 example utterances.  

## <a name="client-application-next-steps"></a>Volgende stappen voor clienttoepassing

Nadat LUIS de JSON-reactie heeft geretourneerd, is LUIS klaar met deze aanvraag. LUIS geeft geen antwoord op deze utterances van gebruikers, maar stelt alleen vast om wat voor soort informatie er wordt gevraagd in natuurlijke taal. De gespreksopvolging wordt uitgevoerd met de clienttoepassing, bijvoorbeeld een Azure-bot. 


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Typen entiteiten](luis-concept-entity-types.md)
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Volgende stappen

This tutorial created a LUIS app, created intents, added example utterances to each intent, added example utterances to the None intent, trained, published, and tested at the endpoint. Dit zijn de basisstappen voor het compileren van een LUIS-model. 

> [!div class="nextstepaction"]
> [Add a decomposable entity to this app](tutorial-machine-learned-entity.md)
