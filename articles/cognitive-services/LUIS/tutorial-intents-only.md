---
title: 'Zelf studie: bedoelingen voors pellen-Luis'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie maakt u een aangepaste app waarmee de intentie van een gebruiker wordt voorspeld. Deze app is het eenvoudigste type LUIS-app omdat de app geen verschillende gegevenselementen uit de utterancetekst extraheert, zoals e-mailadressen of datums.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 1e39126324de486d118f808b37672c9fd08af711
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822764"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Zelf studie: LUIS-app bouwen om de bedoelingen van de gebruiker te bepalen

In deze zelf studie maakt u een aangepaste app waarmee de bedoeling van een gebruiker wordt voor speld op basis van de utterance (text). 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Een nieuwe app maken 
> * Intents maken
> * Voorbeelden van utterances toevoegen
> * App trainen
> * App publiceren
> * Intentie van het eind punt ophalen


[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]

## <a name="user-intentions-as-intents"></a>Gebruikersintenties als intents

Het doel van de app is om de intentie van natuurlijke omgangstaal in een tekst te bepalen: 

`I'd like to order a veggie pizza with a salad on the side.`

Deze intenties worden onderverdeeld in **Intents**. 

|Intentie|Doel|
|--|--|
|`ModifyOrder`|De pizza-volg orde van de gebruiker bepalen.|
|`Greeting`|Begin met bot-gesprek.|
|`ConfirmOrder`|Bevestig de pizza-volg orde.|
|`None`|Bepaal of de gebruiker vraagt om iets dat de app niet moet reageren. Deze intent is geleverd als onderdeel van het maken van de app en kan niet worden verwijderd. |

## <a name="create-a-new-app"></a>Een nieuwe app maken

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Een nieuwe intent maken 

1. Selecteer **+ maken**in de [Luis-Portal](https://preview.luis.ai)van de app in de sectie **bouwen** . Voer de nieuwe naam van de doel groep in die hieronder wordt weer gegeven en selecteer vervolgens **gereed**.

    De `OrderPizza` intentie wordt voor speld wanneer een gebruiker een pizza wil best Ellen. 

1. Voeg verschillende voorbeelden van utterances toe aan deze intent waarnaar een gebruiker waarschijnlijk zal vragen:

    |`OrderPizza` voorbeeld uitingen|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|

    ![Voorbeelden van utterances toevoegen](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Door _voorbeelden van utterances_ op te geven traint u LUIS in het soort utterances dat voor deze intent kan worden voorspeld. 

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]    

## <a name="create-remaining-intents"></a>Resterende intenties maken

1. Maak de `Greeting` intentie en voeg het volgende voor beeld uitingen toe. Dit is het doel om te bepalen of een gebruiker een nieuw pizza-order gesprek start.

    |`Greeting` voorbeeld uitingen|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Maak de `Confirm` intentie en voeg het volgende voor beeld uitingen toe. Dit is de bedoeling om te bepalen of een gebruiker klaar is met het best Ellen en de order gegevens accepteert. 

    |`Confirm` voorbeeld uitingen|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Voor beeld van geen intentie-uitingen

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>De app trainen 

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>De app publiceren 

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)] 

## <a name="get-intent-prediction"></a>Voor spelling van intentie ophalen

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in de adres balk en voer het volgende in:

    `get a medium vegetarian pizza for delivery` 

    Dit is niet precies hetzelfde als een voor beeld van een utterance, dus het is een goede test om te controleren of LUIS kan ontdekken wat er moet worden voor speld met dit doel.

    De laatste parameter van de queryreeks is `q`, de utterance**query**. Deze utterance is niet hetzelfde als een van de voorbeeldutterances. Dit is een goede test die de `OrderPizza`-intent als de best scorende intent moet retourneren. 

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

    De entiteitenmatrix is leeg omdat deze app momenteel geen entiteiten bevat. 

    Het JSON-resultaat identificeert de hoogst scorende intent als **`prediction.topIntent`** -eigenschap. Alle scores liggen tussen 1 en 0, waarbij de betere score dichter bij 1 liggen. 

1. Wijzig de URL- **query** parameter in de **begroetings** intentie:

    `Howdy`

    Dit is niet precies hetzelfde als een voor beeld van een utterance, dus het is een goede test om te controleren of LUIS kan ontdekken wat er moet worden voor speld met dit doel. 

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
 
    Deze voor spelling heeft een betrouwbaarheids Score van 44%. Als u de betrouwbaarheids score wilt verg Roten, voegt u tussen 15 en 30 voorbeeld uitingen toe.  

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

In deze zelf studie hebt u een LUIS-app gemaakt, worden er gemaakte intenties, een voor beeld van een uitingen toegevoegd aan elke intentie, het toegevoegde voor beeld uitingen aan de geen intentie, getraind, gepubliceerd en getest op het eind punt. Dit zijn de basisstappen voor het compileren van een LUIS-model. 

> [!div class="nextstepaction"]
> [Vooraf gemaakte intents en entiteiten aan deze app toevoegen](tutorial-machine-learned-entity.md)
