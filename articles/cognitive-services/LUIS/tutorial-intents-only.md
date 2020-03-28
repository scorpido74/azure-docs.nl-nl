---
title: 'Zelfstudie: Intenties voorspellen - LUIS'
description: In deze zelfstudie maakt u een aangepaste app waarmee de intentie van een gebruiker wordt voorspeld. Deze app is het eenvoudigste type LUIS-app omdat de app geen verschillende gegevenselementen uit de utterancetekst extraheert, zoals e-mailadressen of datums.
ms.topic: tutorial
ms.date: 03/24/2020
ms.openlocfilehash: c58c96f717de77c065d7f844928714eb4fb3e4db
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80286741"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Zelfstudie: Een LUIS-app bouwen om de intenties van gebruikers te bepalen

In deze zelfstudie maakt u een aangepaste app die de intentie van een gebruiker voorspelt op basis van de utterance (tekst).

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Een nieuwe app maken
> * Intents maken
> * Voorbeelden van utterances toevoegen
> * App trainen
> * App publiceren
> * Intent-voorspelling ophalen vanaf eindpunt

## <a name="user-intentions-as-intents"></a>Gebruikersintenties als intents

Het doel van de app is om de intentie van natuurlijke omgangstaal in een tekst te bepalen:

`I'd like to order a veggie pizza with a salad on the side.`

Deze intenties worden onderverdeeld in **Intents**.

|Intentie|Doel|
|--|--|
|`ModifyOrder`|Bepaal de pizzabestelling van de gebruiker.|
|`Greeting`|Begin bot gesprek.|
|`ConfirmOrder`|Bevestig pizza bestelling.|
|`None`|Bepaal of de gebruiker iets vraagt waar de app niet op moet reageren. Deze intent is geleverd als onderdeel van het maken van de app en kan niet worden verwijderd. |

## <a name="create-a-new-app"></a>Een nieuwe app maken

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Een nieuwe intent maken

Een intentie wordt gebruikt om uitingen van gebruikers te classificeren op basis van de intentie van de gebruiker, bepaald op basis van de tekst van de natuurlijke taal.

Om een utterance te classificeren, heeft de intentie voorbeelden nodig van gebruikersuitingen die met deze intentie moeten worden geclassificeerd.

1. Selecteer in de sectie **Bouwen** op de pagina **Intents** de optie **+ Maken** om een nieuwe intentie te maken. Voer de nieuwe `OrderPizza`intentienaam in en selecteer **Gereed**.

    De `OrderPizza` intentie wordt voorspeld wanneer een gebruiker een pizza wil bestellen.

1. Voeg verschillende voorbeelden van utterances toe aan deze intent waarnaar een gebruiker waarschijnlijk zal vragen:

    |`OrderPizza`voorbeelduitingen|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    ![Voorbeelden van utterances toevoegen](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Door _voorbeelduitingen aan_te bieden, leert u LUIS over welke soorten uitingen voor deze intentie moeten worden voorspeld.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Resterende intenties maken

1. Maak `Greeting` de intentie en voeg de volgende voorbeelduitingen toe. Dit is de bedoeling om te bepalen of een gebruiker een nieuw gesprek met pizzabestellingen begint.

    |`Greeting`voorbeelduitingen|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Maak `Confirm` de intentie en voeg de volgende voorbeelduitingen toe. Dit is de bedoeling om te bepalen of een gebruiker klaar is met bestellen en accepteert de ordergegevens.

    |`Confirm`voorbeelduitingen|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>None

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>De app trainen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>De app publiceren

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Intent-voorspelling krijgen

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in de adresbalk en voer het:

    `get a medium vegetarian pizza for delivery`

    Dit is niet precies hetzelfde als een voorbeeld uiting, dus het is een goede test om te zien of LUIS kan leren wat moet worden voorspeld met deze bedoeling.

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

    De array entiteiten is leeg omdat deze app momenteel geen entiteiten heeft (eenheid gegevens in de uiting om te extraheren).

    Het JSON-resultaat identificeert de **`prediction.topIntent`** intentie van de hoogste score als eigenschap. Alle scores liggen tussen 1 en 0, waarbij de betere score dichter bij 1 komt.

1. Wijzig de parameter **URL-query** om de intentie begroeting te **targeten:**

    `Howdy`

    Dit is niet precies hetzelfde als een voorbeeld uiting, dus het is een goede test om te zien of LUIS kan leren wat moet worden voorspeld met deze bedoeling.

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

    Deze voorspelling heeft een betrouwbaarheidsscore van 44%. Als u de betrouwbaarheidsscore wilt verhogen, voegt u tussen de 15 en 30 voorbeelduitingen toe.

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

Deze zelfstudie heeft een LUIS-app gemaakt, intenties gemaakt, voorbeelduitingen aan elke intentie toegevoegd, voorbeelduitingen toegevoegd aan de intentie Geen, getraind, gepubliceerd en getest op het eindpunt. Dit zijn de basisstappen voor het compileren van een LUIS-model.

> [!div class="nextstepaction"]
> [Een onbestaande entiteit toevoegen aan deze app](tutorial-machine-learned-entity.md)
