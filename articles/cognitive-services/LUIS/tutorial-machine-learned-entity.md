---
title: 'Zelfstudie: gestructureerde gegevens extraheren met door de machine geleerde entiteit - LUIS'
titleSuffix: Azure Cognitive Services
description: Haal gestructureerde gegevens uit een utterance met behulp van de door de machine geleerde entiteit. Als u de extractienauwkeurigheid wilt verhogen, voegt u subcomponenten toe met beschrijvingen en beperkingen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: e1709a5e86c8fed8d7f724ad1b105bd02df9fa56
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75381763"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Zelfstudie: Gestructureerde gegevens extraheren uit de uiting van de gebruiker met door machines geleerde entiteiten in Language Understanding (LUIS)

Haal in deze zelfstudie gestructureerde gegevens uit een utterance met behulp van de door de machine geleerde entiteit.

De door de machine geleerde entiteit ondersteunt het [modeldecompositieconcept](luis-concept-model.md#v3-authoring-model-decomposition) door subcomponententiteiten hun beschrijvingen en beperkingen te bieden.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Entiteit met machinegeleerde entiteit toevoegen
> * Subcomponent toevoegen
> * De beschrijving van de subcomponent toevoegen
> * Beperking subcomponent toevoegen
> * App trainen
> * Test-app
> * App publiceren
> * Entiteitsvoorspelling ophalen vanaf eindpunt

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Waarom een door de machine geleerde entiteit gebruiken?

Deze zelfstudie voegt een door de machine geleerde entiteit toe om gegevens uit een utterance te extraheren.

Het doel van een entiteit is om de te extraheren gegevens te definiëren. Dit omvat het geven van de gegevens een naam, een type (indien mogelijk), een resolutie van de gegevens als er ambiguïteit, en de exacte tekst die deel uitmaakt van de gegevens.

Als u de entiteit wilt definiëren, moet u de entiteit maken en vervolgens de tekst labelen die de entiteit in de voorbeeldutterance vertegenwoordigt. Deze gelabelde voorbeelden leren LUIS wat de entiteit is en waar deze kan worden gevonden in een utterance.

## <a name="entity-decomposability-is-important"></a>Entiteitsontmetbaarheid is belangrijk

Entiteitsontmetbaarheid is belangrijk voor zowel intentievoorspelling als voor gegevensextractie.

Begin met een door machines geleerde entiteit, die de entiteit op het begin- en het hoogste niveau is voor gegevensextractie. Ontleed vervolgens de entiteit in de onderdelen die de clienttoepassing nodig heeft.

Hoewel u mogelijk niet weet hoe gedetailleerd u uw entiteit wilt hebben wanneer u uw app begint, is het een beste manier om te beginnen met een door machines geleerde entiteit en vervolgens te ontleden met subcomponenten naarmate uw app rijpt.

In de praktijk maakt u een door de machine geleerde entiteit om een bestelling voor een pizza-app weer te geven. De bestelling moet alle onderdelen die nodig zijn om de bestelling fullfil. Om te beginnen zal de entiteit ordergerelateerde tekst extraheren, grootte en hoeveelheid eruit trekken.

Een `Please deliver one large cheese pizza to me` uiting `one large cheese pizza` voor moet extraheren als `1` `large`de volgorde, dan ook extraheren en .

Er is verdere ontbinding die u toevoegen, zoals het maken van subcomponenten voor toppings of korst. Na deze zelfstudie moet u er zeker `Order` van zijn dat deze subcomponenten aan uw bestaande entiteit worden toegevoegd.

## <a name="import-example-json-to-begin-app"></a>Voorbeeld .json importeren om app te starten

1.  Download en sla het [JSON-bestand van](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)de app op.

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Tekst labelen als entiteiten in voorbeelduitingen

Als u details wilt extraheren over een pizzaorder, maakt u een entiteit op het hoogste niveau, door de machine geleerde `Order` entiteit.

1. Selecteer op de pagina **Intents** de intentie **OrderPizza.**

1. Selecteer in de lijst met voorbeelduitingen de volgende utterance.

    |Uitutterance voor het voorbeeld van volgorde|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Begin met het selecteren net `pickup` voor de meest linkse tekst van (#1), ga dan net verder dan de meest rechtse tekst (#2 `anchovies` - hiermee eindigt het labelingsproces). Er verschijnt een pop-upmenu. Voer in het pop-upvak de naam `Order` van de entiteit in als (#3). Selecteer `Order - Create new entity` vervolgens in de lijst (#4).

    ![Label begin en einde van tekst voor volledige bestelling](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Een entiteit zal niet altijd de hele uiting zijn. `pickup` Geeft in dit specifieke geval aan hoe de bestelling moet worden ontvangen. Vanuit een conceptueel perspectief moet `pickup` deel uitmaken van de gelabelde entiteit voor de order.

1. Selecteer **Structuur toevoegen** en selecteer **Volgende**in het vak **Een entiteitstype kiezen** . Structuur is nodig om subcomponenten zoals grootte en hoeveelheid toe te voegen.

    ![Structuur toevoegen aan entiteit](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Voeg in het vak Een machine geleerde entiteit `Size` **maken** in het vak **Structuur** toe en selecteer Enter.
1. Als u een **beschrijving**wilt `+` toevoegen, selecteert u de in het gebied **Descriptoren voor Grootte** en selecteert u **Vervolgens Nieuwe woordgroeplijst maken**.

1. Voer in het vak Nieuwe **woordgroep descriptor maken** `small`de `medium`naam `large` `SizeDescriptor` in en voer waarden in van: , en . Wanneer het vak **Suggesties** wordt `extra large`invult, selecteert u en `xl`. Selecteer **Gereed** om de lijst met nieuwe woordgroepen te maken.

    Deze woordgroep lijst beschrijving `Size` helpt de subcomponent te vinden woorden met betrekking tot grootte door het te voorzien van voorbeeldwoorden. Deze lijst hoeft niet elke grootte woord op te nemen, maar moet woorden bevatten die naar verwachting de grootte aan te geven.

    ![Een beschrijving maken voor de subcomponent grootte](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Selecteer in het venster Een machine geleerde entiteit `Size` **maken** de optie **Maken** om de subcomponent te maken.

    De `Order` entiteit `Size` met een component `Order` wordt gemaakt, maar alleen de entiteit is toegepast op de utterance. U moet de `Size` entiteitstekst labelen in de voorbeeldutterance.

1. Label in dezelfde voorbeeldutterance de subcomponent **Grootte** van `large` door het woord te selecteren en vervolgens de entiteit **Grootte** te selecteren in de vervolgkeuzelijst.

    ![Label de grootteentiteit voor tekst in de utterance.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    De regel is effen onder de tekst omdat zowel de etikettering als de voorspelling overeenkomen omdat u de tekst expliciet hebt gelabeld.

1. Label `Order` de entiteit in de resterende uitingen samen met de grootteentiteit. De vierkante haakjes in de tekst `Order` geven `Size` de gelabelde entiteit en de entiteit binnenaan aan.

    |Voorbeelduitingen bestellen|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Entiteiten en subcomponenten maken in alle resterende voorbeelduitingen.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Hoe ga je om met `a` impliciete gegevens zoals de brief die een enkele pizza impliceert? Of het `pickup` ontbreken `delivery` van en om aan te geven waar de pizza wordt verwacht? Of het ontbreken van een grootte om uw standaardgrootte van klein of groot aan te geven? Overweeg om impliciete gegevensverwerking te behandelen als onderdeel van uw bedrijfsregels in de clienttoepassing in plaats van of in aanvulling op LUIS.

1. Als u de app wilt trainen, selecteert u **Trainen**. Training past de wijzigingen, zoals de nieuwe entiteiten en de gelabelde uitingen, toe op het actieve model.

1. Voeg na de training een nieuw voorbeelduiting toe aan de intentie om te zien hoe goed LUIS de door de machine geleerde entiteit begrijpt.

    |Uitutterance voor het voorbeeld van volgorde|
    |--|
    |`pickup XL meat lovers pizza`|

    De algemene topentiteit wordt `Order` gelabeld `Size` en de subcomponent is ook gelabeld met stippellijnen. Dit is een succesvolle voorspelling.

    ![Nieuwe voorbeeldutterance voorspeld met entiteit](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    De stippellijn geeft de voorspelling aan.

1. Als u de voorspelling wilt wijzigen in een gelabelde entiteit, selecteert u de rij en selecteert u **Entiteitsvoorspellingen bevestigen**.

    ![Accepteer voorspelling door entiteitsvoorspelling bevestigen te selecteren.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Op dit punt werkt de door de machine geleerde entiteit omdat deze de entiteit kan vinden in een nieuwe voorbeeldutterance. Terwijl u voorbeelduitingen toevoegt, labelt u, als de entiteit niet correct wordt voorspeld, de entiteit en de subcomponenten. Als de entiteit correct wordt voorspeld, moet u de voorspellingen bevestigen.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Vooraf opgebouwd nummer toevoegen om gegevens te extraheren

De orderinformatie moet ook bevatten hoeveel van een artikel in de volgorde staat, zoals het aantal pizza's. Om deze gegevens te extraheren, moet een nieuwe `Order` machine-geleerde subcomponent worden toegevoegd en dat onderdeel heeft een beperking van een vooraf gebouwd getal nodig. Door de entiteit te beperken tot een vooraf opgebouwd getal, vindt en `2`haalt de `two`entiteit getallen aan of de tekst een cijfer is, of tekst, .

Begin met het toevoegen van de vooraf gebouwde nummerentiteit aan de app.

1. Selecteer **Entiteiten** in het linkermenu en selecteer **+ Vooraf gebouwde entiteit toevoegen**.

1. Zoek in het vak **Vooraf gebouwde entiteiten toevoegen** naar en selecteer **nummer** en selecteer **Gereed**.

    ![Vooraf gebouwde entiteit toevoegen](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    De vooraf gebouwde entiteit wordt toegevoegd aan de app, maar is nog geen beperking.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Subcomponententiteit maken met beperking om gegevens te extraheren

De `Order` entiteit moet `Quantity` een subcomponent hebben om te bepalen hoeveel van een artikel in de volgorde staat. De hoeveelheid moet worden beperkt tot een getal, zodat de geëxtraheerde gegevens onmiddellijk bruikbaar zijn voor de clienttoepassing.

Een beperking wordt toegepast als een tekstovereenkomst, hetzij met exacte matching (zoals een lijstentiteit) of via reguliere expressies (zoals een entiteit met reguliere expressies of een vooraf gebouwde entiteit).

Door een beperking te gebruiken, wordt alleen tekst die overeenkomt met die beperking, geëxtraheerd.

1. Selecteer **Entiteiten** en `Order` selecteer vervolgens de entiteit.
1. Selecteer **+ Component toevoegen** `Quantity` en voer de naam in en selecteer Enter om de nieuwe entiteit aan de app toe te voegen.
1. Selecteer na de succesmelding de `Quantity` subcomponent en selecteer vervolgens het potlood Beperking.
1. Selecteer in de vervolgkeuzelijst het vooraf gebouwde getal.

    ![Maak een entiteit met vooraf opgebouwd nummer als beperking.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    De `Quantity` entiteit wordt toegepast als en alleen als er tekst wordt gevonden die overeenkomt met de vooraf gebouwde getalentiteit.

    De entiteit met de beperking wordt gemaakt, maar nog niet toegepast op de voorbeelduitingen.

    > [!NOTE]
    > Een subcomponent kan worden genest in een subcomponent tot 5 niveaus. Hoewel dit niet wordt weergegeven in dit artikel, is het beschikbaar via de portal en de API.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Voorbeeldvan label om LUIS over de entiteit te leren

1. Selecteer **Intents** in de navigatie links en selecteer vervolgens de intentie **OrderPizza.** De drie getallen in de volgende uitingen worden gelabeld, maar staan visueel onder de `Order` entiteitsregel. Dit lagere niveau betekent dat de entiteiten worden `Order` gevonden, maar niet worden beschouwd als afgezien van de entiteit.

    ![Vooraf opgebouwd nummer wordt gevonden, maar nog niet beschouwd als afgezien van de entiteit Order.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Label de getallen `Quantity` met de `2` entiteit door de `Quantity` in de voorbeeldutterance te selecteren en vervolgens in de lijst te selecteren. Label `6` de `1` en de in hetzelfde voorbeeld uiting.

    ![Labeltekst met entiteit van hoeveelheid.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>De app trainen om de entiteitswijzigingen toe te passen op de app

Selecteer **Trainen** om de app te trainen met deze nieuwe uitingen.

![Train de app en bekijk vervolgens de voorbeelduitingen.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Op dit punt bevat de volgorde enkele details die kunnen worden geëxtraheerd (grootte, hoeveelheid en totale ordertekst). Er is verdere raffinage `Order` van de entiteit, zoals pizza toppings, type korst, en bijbestellingen. Elk van deze moet worden gemaakt `Order` als subcomponenten van de entiteit.

## <a name="test-the-app-to-validate-the-changes"></a>De app testen om de wijzigingen te valideren

Test de app met het interactieve **testpaneel.** Met dit proces u een nieuwe utterance invoeren en vervolgens de voorspellingsresultaten bekijken om te zien hoe goed de actieve en getrainde app werkt. De intentievoorspelling moet vrij zelfverzekerd zijn (boven 70%) en de entiteitsextractie moet `Order` ten minste de entiteit oppikken. De details van de orderentiteit ontbreken mogelijk omdat 5 uitingen niet genoeg zijn om elk geval af te handelen.

1. Selecteer **Test** in de bovenste navigatiebalk.
1. Voer de `deliver a medium veggie pizza` utterance in en selecteer Enter. Het actieve model voorspelde de juiste intentie met meer dan 70% vertrouwen.

    ![Voer een nieuwe utterance in om de intentie te testen.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Selecteer **Inspecteren** om de entiteitsvoorspellingen te bekijken.

    ![Bekijk de entiteitsvoorspellingen in het interactieve testpaneel.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    De grootte is correct geïdentificeerd. Houd er rekening mee `OrderPizza` dat de voorbeelduitingen in `medium` de intentie geen voorbeeld hebben van `SizeDescriptor` als grootte, maar wel een beschrijving van een woordgroeplijst met medium.

    De hoeveelheid is niet correct voorspeld. Als u dit wilt oplossen, u meer voorbeelduitingen toevoegen met `Quantity` dat woord om de hoeveelheid aan te geven en dat woord als een entiteit te labelen.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>De app publiceren om deze te openen vanaf het HTTP-eindpunt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Intentie en entiteitsvoorspelling ophalen van HTTP-eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in het adres en voer dezelfde query in als u in het interactieve testpaneel hebt ingevoerd.

    `deliver a medium veggie pizza`

    De laatste querytekenreeksparameter is `query`de **utterancequery**.

    ```json
    {
        "query": "deliver a medium veggie pizza",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
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


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Zelfstudie - intents](luis-quickstart-intents-only.md)
* [Concept - entiteiten](luis-concept-entity-types.md) conceptuele informatie
* [Concept - beschikt over](luis-concept-feature.md) conceptuele informatie
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie gebruikt de app een door de machine geleerde entiteit om de intentie van de utterance van een gebruiker te vinden en details uit die utterance te extraheren. Met behulp van de door de machine geleerde entiteit u de details van de entiteit ontleden.

> [!div class="nextstepaction"]
> [Een vooraf gemaakte entiteit KeyPhrase toevoegen](luis-quickstart-intent-and-key-phrase.md)
