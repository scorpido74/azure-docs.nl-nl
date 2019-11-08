---
title: 'Zelf studie: gestructureerde gegevens ophalen met door de machine geleerde entiteit-LUIS'
titleSuffix: Azure Cognitive Services
description: Gestructureerde gegevens uit een utterance extra heren met behulp van de door de machine geleerde entiteit. Voeg subonderdelen met descriptoren en beperkingen toe om de nauw keurigheid van de extractie te verg Roten.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: diberry
ms.openlocfilehash: 36b75f33b4fc9062d09fbc670a509594142f09bd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828525"
---
# <a name="tutorial-extract-structured-data-with-machine-learned-entities-in-language-understanding-luis"></a>Zelf studie: gestructureerde gegevens met door machines geleerde entiteiten extra heren in Language Understanding (LUIS)

In deze zelf studie extraheert u gestructureerde gegevens uit een utterance met behulp van de door de machine geleerde entiteit. 

De door de machine geleerde entiteit ondersteunt het model voor het [decompositieen van modellen](luis-concept-model.md#v3-authoring-model-decomposition) door subcomponent entiteiten te voorzien van hun descriptor en beperkingen. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Door machine geleerde entiteit toevoegen 
> * Subonderdeel toevoegen
> * De descriptor van een subonderdeel toevoegen
> * De beperking van een subonderdeel toevoegen
> * App trainen
> * App testen
> * App publiceren
> * De voor spelling van de entiteit van het eind punt ophalen

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Waarom een door de machine geleerde entiteit gebruiken?

In deze zelf studie wordt een door een machine geleerde entiteit toegevoegd voor het extra heren van gegevens uit een utterance. 

Het doel van een entiteit is het definiëren van de gegevens die moeten worden geëxtraheerd. Dit omvat het geven van de gegevens een naam, een type (indien mogelijk), een oplossing van de gegevens als er sprake is van dubbel zinnigheid en de exacte tekst die de gegevens oplevert. 

Als u de entiteit wilt definiëren, moet u de entiteit maken en vervolgens labelen van de tekst die de entiteit vertegenwoordigt in het voor beeld utterance. Deze labels hebben een voor beeld van LUIS wat de entiteit is en waar deze kan worden gevonden in een utterance. 

## <a name="entity-decomposability-is-important"></a>Het desamen stellen van de entiteit is belang rijk

Het afstellen van de entiteit is belang rijk voor zowel de voor spelling als voor de gegevens extractie. 

Begin met een door de machine geleerde entiteit, die de begin-en de entiteit op het hoogste niveau is voor het uitpakken van gegevens. Splits de entiteit vervolgens in de onderdelen die nodig zijn voor de client toepassing. 

Hoewel u mogelijk niet weet hoe nauw keurig uw entiteit moet zijn wanneer u uw app start, moet een best practice beginnen met een door de machine geleerde entiteit en vervolgens afbreken met subonderdelen als uw app is gerijpt.

In de praktijk moet u een door de machine geleerde entiteit maken die een bestelling voor een pizza-app vertegenwoordigt. De volg orde moet alle onderdelen bevatten die nodig zijn om de volg orde te fullfilen. Om te beginnen bevat de entiteit alle teksten met betrekking tot de volg orde en de specifieke grootte en hoeveelheid. 

Een utterance voor `deliver one large cheese pizza` moet de volledige utterance als de order extra heren en vervolgens `1` en `large`uitpakken. 

U kunt verdere afbraak doen, zoals toppings of crust. Na deze zelf studie moet u er zeker van zijn dat deze subonderdelen worden toegevoegd aan uw bestaande `Order`-entiteit.

## <a name="import-example-json-to-begin-app"></a>Voor beeld van import. json voor het starten van de app

1.  Down load en sla het [JSON-bestand](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)van de app op.

1. Selecteer in de [Preview Luis-Portal](https://preview.luis.ai)op de pagina **mijn apps** **importeren**en vervolgens **importeren als JSON**. Zoek het opgeslagen JSON-bestand uit de vorige stap. U hoeft de naam van de app niet te wijzigen. Selecteer **gereed**

1. Selecteer in de sectie **beheren** op het tabblad **versies** de versie, selecteer **klon** om de versie te klonen en geef deze de naam `mach-learn`. Selecteer vervolgens **gereed** om het kloon proces te volt ooien. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

    > [!TIP] 
    > Klonen is een best practice voordat u uw app wijzigt. Wanneer u klaar bent met een versie, exporteert u een versie, als een. json-of. lu-bestand, en controleert u of het bron beheer is voltooid.

1. Selecteer **samen stellen** en **intenties** om de belangrijkste bouw stenen van een Luis-app, de bedoeling, te bekijken.

    ![Ga van de pagina versies naar de pagina intenties.](media/tutorial-machine-learned-entity/new-version-imported-app.png)

## <a name="label-text-as-entities-in-example-utterances"></a>Tekst labelen als entiteiten in voor beeld uitingen

Als u details over een pizza order wilt extra heren, maakt u een hoofd niveau, door de machine geleerd `Order` entiteit.

1. Selecteer op de pagina **intenties** de **OrderPizza** intentie. 

1. Selecteer in de lijst voor beeld uitingen de volgende utterance. 

    |Voor beeld van een bestelling utterance|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Begin met het selecteren van net vóór de meest linkse tekst van `pickup` (#1) en ga vervolgens alleen verder dan de juiste tekst, `anchovies` (#2: Hiermee wordt het label proces beëindigd). Er wordt een pop-upmenu weer gegeven. Voer in het pop-upvenster de naam van de entiteit in als `Order` (#3). Selecteer vervolgens `Order - Create new entity` in de lijst (#4).

    ![Label begint en eindigt op tekst voor volledige volg orde](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Een entiteit is niet altijd de hele utterance. In dit specifieke geval geeft `pickup` aan hoe de order moet worden ontvangen, zodat deze deel moet uitmaken van de gelabelde entiteit voor de order. 

1. Selecteer in het vak **een entiteits type kiezen** de optie **structuur toevoegen** en selecteer vervolgens **volgende**. De structuur is nodig om subonderdelen, zoals grootte en hoeveelheid, mogelijk te maken.

    ![Structuur toevoegen aan entiteit](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. In het vak **een door een machine geleerde entiteit maken** , in het vak **structuur** , voegt u `Size` Selecteer vervolgens ENTER. 
1. Als u een **descriptor**wilt toevoegen, selecteert u de `+` in het gedeelte descriptors **voor grootte** en selecteert u **nieuwe woordgroepen lijst maken**.

1. Voer in het vak **nieuwe woordgroepen lijst descriptor maken** de naam in `SizeDescriptor` Voer waarden in: `small`, `medium`en `large`. Wanneer het vak Voorst **Ellen** invult, selecteert u `extra large`en `xl`. Selecteer **gereed** om de nieuwe woordgroepen lijst te maken. 

    Deze woordgroepen lijst descriptor helpt het subonderdeel `Size` woorden te vinden die gerelateerd zijn aan de grootte door een voor beeld van een woord op te geven. In deze lijst hoeft u niet elk woord grootte op te geven, maar moet woorden bevatten die worden verwacht om de grootte aan te duiden. 

    ![Een descriptor maken voor het subonderdeel grootte](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Selecteer in het venster een door de **computer geleerde entiteit maken** de optie **maken** om het subonderdeel `Size` te maken.  

    De `Order` entiteit met een `Size` onderdeel wordt gemaakt, maar alleen de `Order` entiteit is toegepast op de utterance. U moet de tekst van de `Size` entiteit labelen in het voor beeld utterance. 

1. In hetzelfde voor beeld utterance labelt u het subonderdeel **grootte** van `large` door het woord te selecteren en vervolgens de entiteit **grootte** te selecteren in de vervolg keuzelijst. 

    ![Label de entiteit grootte voor tekst in het utterance.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    De lijn wordt onder de tekst weer, omdat het labelen en de voor spelling overeenkomen omdat u de tekst expliciet labelt.

1. Voorzie de `Order` entiteit in de resterende uitingen samen met de grootte-entiteit. De vier Kante haken in de tekst geven de gelabelde `Order` entiteit en de `Size` entiteit binnen.

    |Voor beeld van een bestelling uitingen|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Maak de entiteiten en subonderdelen in alle resterende voor beeld-uitingen.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Hoe behandel ik geïmpliceerde gegevens, zoals de letter `a` het impliceren van één pizza? Of het ontbreken van `pickup` en `delivery` om aan te geven waar de pizza wordt verwacht? Of het ontbreken van een grootte om uw standaard grootte of klein of groot te geven? Overweeg de verwerking van geïmpliceerde gegevens als onderdeel van uw bedrijfs regels in de client toepassing. 

1. Als u de app wilt trainen, selecteert u **trainen**. Training past de wijzigingen, zoals de nieuwe entiteiten en het gelabelde uitingen, toe op het actieve model.

1. Voeg na de training een nieuwe voorbeeld utterance toe om te begrijpen hoe goed LUIS de door de machine geleerde entiteit begrijpt. 

    |Voor beeld van een bestelling utterance|
    |--|
    |`pickup XL meat lovers pizza`|

    De algemene bovenste entiteit, `Order` is gelabeld en het subonderdeel `Size` wordt ook aangeduid met stippel lijnen. Dit is een geslaagde voor spelling. 

    ![Nieuw voor beeld utterance voorspeld met entiteit](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    De gestippelde koppeling geeft de voor spelling aan. 

1. Als u de voor spelling in een entiteit met een label wilt wijzigen, selecteert u de rij en selecteert u voor **spellingen van entiteit bevestigen**.

    ![Accepteer de voor spelling door entiteits voorspelling bevestigen te selecteren.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Op dit moment werkt de door de machine geleerde entiteit goed, omdat deze de entiteit kan vinden in een nieuw voor beeld utterance. Als u voor beeld uitingen toevoegt en de entiteit niet correct is voor speld, labelt u de entiteit en de subonderdelen. Als de entiteit correct wordt voor speld, moet u ervoor zorgen dat u de voor spellingen bevestigt. 

## <a name="add-prebuilt-number-to-app-to-help-extract-data"></a>Een vooraf gebouwd nummer toevoegen aan de app om gegevens te extra heren

De order gegevens moeten ook betrekking hebben op het aantal van een item in de volg orde, zoals het aantal pizzas. Als u deze gegevens wilt extra heren, moet er een nieuw apparaat worden toegevoegd aan `Order` en dat onderdeel een beperking van een vooraf gedefinieerd nummer nodig heeft. Door de entiteit te beperken tot een vooraf samengesteld nummer, wordt door de entiteit getallen gevonden en opgehaald, ongeacht of de tekst een cijfer, `2`of tekst `two`is.

Begin door het vooraf gedefinieerde nummer toe te voegen aan de app. 

1. Selecteer **entiteiten** in het menu links en selecteer vervolgens een **vooraf samengestelde entiteit toevoegen**. 

1. Zoek in het vak **vooraf gemaakte entiteiten toevoegen** naar en selecteer **nummer** en selecteer vervolgens **gereed**. 

    ![Vooraf samengestelde entiteit toevoegen](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    De vooraf samengestelde entiteit wordt toegevoegd aan de app, maar is nog geen beperking. 

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Een subcomponent entiteit met een beperking maken om gegevens te extra heren

De `Order` entiteit moet een subonderdeel `Quantity` hebben om te bepalen hoeveel van een item in de volg orde is. De hoeveelheid moet worden beperkt tot een getal zodat de geëxtraheerde gegevens direct kunnen worden gebruikt door de client toepassing. 

Een beperking wordt toegepast als tekst overeenkomst, hetzij met exacte overeenkomst (zoals een lijst entiteit) of via reguliere expressies (zoals een reguliere expressie-entiteit of een vooraf gedefinieerde entiteit). 

1. Selecteer **entiteiten** en selecteer vervolgens de entiteit `Order`. 
1. Selecteer **+ onderdeel toevoegen** en voer de naam `Quantity` Selecteer vervolgens ENTER om de nieuwe entiteit toe te voegen aan de app.
1. Nadat u de melding hebt ontvangen, selecteert u het subonderdeel `Quantity` en selecteert u vervolgens de beperkings potlood.
1. Selecteer in de vervolg keuzelijst het vooraf gemaakte nummer. 

    ![Hoeveelheids entiteit maken met vooraf gebouwd nummer als beperking.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    De entiteit met de beperking wordt gemaakt, maar is nog niet toegepast op het voor beeld uitingen.

    > [!NOTE]
    > Een subonderdeel kan Maxi maal vijf niveaus worden genest in een subonderdeel. Hoewel dit niet wordt weer gegeven in dit artikel, is het beschikbaar via de portal en de API.  

## <a name="label-example-utterance-with-subcomponent-for-quantity-to-teach-luis-about-the-entity"></a>Label voorbeeld utterance met subonderdeel voor hoeveelheid om LUIS over de entiteit te leren

1. Selecteer **intenties** in de linkernavigatiebalk en selecteer vervolgens de **OrderPizza** intentie. De drie getallen in de volgende uitingen zijn gelabeld, maar zijn visueel onder de `Order` entiteits regel. Dit lagere niveau betekent dat de entiteiten worden gevonden, maar niet worden meegenomen in de `Order` entiteit.

    ![Het vooraf ontwikkelde nummer is gevonden, maar wordt nog niet meegenomen in de order entiteit.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Voorzie de nummers van de entiteit `Quantity` door de `2` te selecteren in het voor beeld utterance en vervolgens `Quantity` te selecteren in de lijst. Voorzie de `6` en het `1` in hetzelfde voor beeld utterance.

    ![Label tekst met entiteit hoeveelheid.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)  

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>De app trainen om de entiteits wijzigingen toe te passen op de app

Selecteer **trainen** om de app te trainen met deze nieuwe uitingen.

![Train de app en Bekijk het voor beeld uitingen.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Op dit moment bevat de order enkele details die kunnen worden geëxtraheerd (grootte, hoeveelheid en totale order tekst). De `Order` entiteit, zoals pizza toppings, type Crust en side orders, wordt verder verfijnd. Elk van deze moet worden gemaakt als subonderdelen van de entiteit `Order`. 

## <a name="test-the-app-to-validate-the-changes"></a>De app testen om de wijzigingen te valideren

Test de app met behulp van het interactieve **test** paneel. Met dit proces kunt u een nieuwe utterance invoeren en vervolgens de resultaten van de voor spelling weer geven om te zien hoe goed de actieve en getrainde app werkt. De intentie voorspelling moet redelijk zijn verzekerd (meer dan 70%) en de extractie van de entiteit moet ten minste de `Order` entiteit ophalen. De details van de order entiteit ontbreken mogelijk omdat er niet genoeg vijf uitingen zijn om elke case af te handelen.

1. Selecteer **Test** in de bovenste navigatiebalk.
1. Voer de utterance-`deliver a medium veggie pizza` in en selecteer ENTER. Het actieve model heeft de juiste intentie gedicteerd met meer dan 70% betrouw baarheid. 

    ![Voer een nieuwe utterance in om de intentie te testen.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Selecteer **controleren** om de voor spellingen van de entiteit weer te geven.

    ![Bekijk de voor spellingen van de entiteit in het interactieve test paneel.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    De grootte is correct geïdentificeerd. Houd er rekening mee dat het voor beeld uitingen in de `OrderPizza` intentie geen voor beeld van `medium` als grootte hebben, maar wel een descriptor van een `SizeDescriptor` woordgroepen lijst bevatten die medium bevat.

    De hoeveelheid is niet juist voor speld. Om dit probleem op te lossen, kunt u meer voor beelden van uitingen toevoegen met behulp van dat woord om de hoeveelheid aan te geven en het label dat woord als `Quantity` entiteit. 

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>De app publiceren voor toegang tot het HTTP-eind punt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction--from-http-endpoint"></a>Intentie en entiteits voorspelling van het HTTP-eind punt ophalen

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in het adres en voer dezelfde query in als u hebt opgegeven in het interactieve test paneel. 

    `deliver a medium veggie pizza`

    De laatste parameter van de queryreeks is `query`, de utterance **query**. 

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

* [Zelf studie-intents](luis-quickstart-intents-only.md)
* Concept informatie over [concept-entiteiten](luis-concept-entity-types.md)
* Conceptuele informatie over [concept functies](luis-concept-feature.md)
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie gebruikt de app een door de machine geleerde entiteit om het doel van de utterance van een gebruiker te vinden en gegevens uit die utterance uit te pakken. Door de door de machine geleerde entiteit te gebruiken, kunt u de details van de entiteit afbreken.  

> [!div class="nextstepaction"]
> [Een vooraf gemaakte entiteit KeyPhrase toevoegen](luis-quickstart-intent-and-key-phrase.md)
