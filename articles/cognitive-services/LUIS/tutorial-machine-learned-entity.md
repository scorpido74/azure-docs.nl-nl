---
title: 'Zelf studie: gestructureerde gegevens ophalen met door de machine geleerde entiteit-LUIS'
description: Gestructureerde gegevens uit een utterance extra heren met behulp van de door de machine geleerde entiteit. Voeg subonderdelen met descriptoren en beperkingen toe om de nauw keurigheid van de extractie te verg Roten.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 52bf2fb0b9f37e0c731a46c0aaf8b6c5e7f0e911
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80545858"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Zelf studie: gestructureerde gegevens ophalen van gebruikers utterance met entiteiten die door machines zijn geleerd in Language Understanding (LUIS)

In deze zelf studie extraheert u gestructureerde gegevens uit een utterance met behulp van de door de machine geleerde entiteit.

De door de machine geleerde entiteit ondersteunt het model voor het [decompositieen van modellen](luis-concept-model.md#v3-authoring-model-decomposition) door subcomponent entiteiten te voorzien van hun descriptor en beperkingen.

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

De entiteit definieert de gegevens die moeten worden geëxtraheerd uit de utterance. Dit omvat het geven van de gegevens een naam, een type (indien mogelijk), een oplossing van de gegevens als er sprake is van dubbel zinnigheid en de exacte tekst die de gegevens oplevert.

Als u de entiteit wilt definiëren, moet u de entiteit maken en vervolgens labelen van de tekst die de entiteit vertegenwoordigt in het voor beeld uitingen binnen alle intenties. Deze labels hebben een voor beeld van LUIS wat de entiteit is en waar deze kan worden gevonden in een utterance.

## <a name="entity-decomposability-is-important"></a>Het desamen stellen van de entiteit is belang rijk

Het afstellen van de entiteit is belang rijk voor zowel de voor spelling als voor gegevens extractie met de entiteit.

Begin met een door de machine geleerde entiteit, die de begin-en de entiteit op het hoogste niveau is voor het uitpakken van gegevens. Splits de entiteit vervolgens in de onderdelen die nodig zijn voor de client toepassing.

Hoewel u mogelijk niet weet hoe nauw keurig uw entiteit moet zijn wanneer u uw app start, moet een best practice beginnen met een door de machine geleerde entiteit en vervolgens afbreken met subonderdelen als uw app is gerijpt.

In dit kunt u een door de machine geleerde entiteit maken die een bestelling voor een pizza-app vertegenwoordigt. De volg orde moet alle onderdelen bevatten die nodig zijn om de volg orde te fullfilen. Als u wilt beginnen, haalt de entiteit order-gerelateerde tekst op, wordt de grootte en hoeveelheid opgehaald.

Een utterance voor `Please deliver one large cheese pizza to me` moet worden `one large cheese pizza` geëxtraheerd als de order en vervolgens ook `1` worden `large`opgehaald en.

U kunt verdere afbraak toevoegen, zoals het maken van subonderdelen voor toppings of crust. Na deze zelf studie moet u er zeker van zijn dat deze subonderdelen aan `Order` uw bestaande entiteit worden toegevoegd.

## <a name="import-example-json-to-begin-app"></a>Voor beeld van import. json voor het starten van de app

1.  Down load en sla het [JSON-bestand](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)van de app op.

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Tekst labelen als entiteiten in voor beeld uitingen

Als u details over een pizza order wilt extra heren, maakt u een op het `Order` hoogste niveau, door de machine geleerde entiteit.

1. Selecteer op de pagina **intenties** de **OrderPizza** intentie.

1. Selecteer in de lijst voor beeld uitingen de volgende utterance.

    |Voor beeld van een bestelling utterance|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Begin met het selecteren van net vóór de meest linkse tekst van `pickup` (#1) en ga vervolgens alleen verder dan de juiste `anchovies` tekst, (#2: Hiermee wordt het label proces beëindigd). Er wordt een pop-upmenu weer gegeven. Voer in het pop-upvenster de naam van de entiteit in `Order` (#3). Selecteer `Order - Create new entity` vervolgens in de lijst (#4).

    ![Label begint en eindigt op tekst voor volledige volg orde](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Een entiteit is niet altijd de hele utterance. In dit specifieke geval `pickup` geeft aan hoe de bestelling moet worden ontvangen. Van een conceptueel perspectief `pickup` moeten deel uitmaken van de gelabelde entiteit voor de order.

1. Selecteer in het vak **een entiteits type kiezen** de optie **structuur toevoegen** en selecteer vervolgens **volgende**. Structuur is nodig om subonderdelen, zoals grootte en hoeveelheid, toe te voegen.

    ![Structuur toevoegen aan entiteit](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. In het vak **een door een machine geleerde entiteit maken** , in het `Size` vak **structuur** , selecteert u vervolgens ENTER.
1. Als u een beschrijving wilt toevoegen, `+` selecteert u in **het gebied** **descriptors**en selecteert u **nieuwe woordgroepen lijst maken**.

1. Voer in het vak **nieuwe woordgroepen lijst descriptor maken** de naam `SizeDescriptor` in en voer vervolgens waarden `small`in `medium`:, `large`en. Wanneer in het vak **suggesties** wordt ingevuld, `extra large`selecteert u `xl`en. Selecteer **gereed** om de nieuwe woordgroepen lijst te maken.

    Deze woordgroepen lijst descriptor helpt `Size` het subonderdeel te zoeken naar woorden die betrekking hebben op de grootte door bijvoorbeeld woorden op te geven. In deze lijst hoeft u niet elk woord grootte op te geven, maar moet woorden bevatten die worden verwacht om de grootte aan te duiden.

    ![Een descriptor maken voor het subonderdeel grootte](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Selecteer in het venster **een door een machine geleerde entiteit maken** de optie `Size` **maken** om het subonderdeel te maken.

    De `Order` entiteit met een `Size` onderdeel wordt gemaakt, maar alleen `Order` de entiteit is toegepast op de utterance. U moet de `Size` entiteits tekst labelen in het voor beeld utterance.

1. In hetzelfde voor beeld utterance labelt u **Size** het subonderdeel grootte `large` van door het woord te selecteren en vervolgens de entiteit **grootte** te selecteren in de vervolg keuzelijst.

    ![Label de entiteit grootte voor tekst in het utterance.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    De lijn wordt onder de tekst weer, omdat het labelen en de voor spelling overeenkomen omdat u de tekst _expliciet_ labelt.

1. Label de `Order` entiteit in de resterende uitingen samen met de grootte-entiteit. De vier Kante haken in de tekst geven de gelabelde `Order` entiteit `Size` en de entiteit binnen.

    |Voor beeld van een bestelling uitingen|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Maak de entiteiten en subonderdelen in alle resterende voor beeld-uitingen.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Hoe behandel ik geïmpliceerde gegevens zoals de letter `a` die een enkele pizza impliceert? Of het ontbreken van `pickup` en `delivery` om aan te geven waar de pizza wordt verwacht? Of het ontbreken van een grootte om de standaard grootte van klein of groot te geven? Overweeg impliciete verwerking van gegevens te behandelen als onderdeel van uw bedrijfs regels in de client toepassing in plaats van of naast LUIS.

1. Als u de app wilt trainen, selecteert u **trainen**. Training past de wijzigingen, zoals de nieuwe entiteiten en het gelabelde uitingen, toe op het actieve model.

1. Voeg na de training een nieuw voor beeld-utterance toe aan het doel om te zien hoe goed LUIS de door de machine geleerde entiteit begrijpt.

    |Voor beeld van een bestelling utterance|
    |--|
    |`pickup XL meat lovers pizza`|

    De algemene bovenste entiteit, `Order` heeft het label en het `Size` subonderdeel wordt ook gelabeld met stippel lijnen.

    ![Nieuw voor beeld utterance voorspeld met entiteit](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    De stippel lijn geeft de voor spelling aan.

1. Als u de voor spelling wilt wijzigen in een entiteit met een label, selecteert u de rij en selecteert u voor **spellingen van entiteit bevestigen**.

    ![Accepteer de voor spelling door entiteits voorspelling bevestigen te selecteren.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Op dit moment werkt de door de machine geleerde entiteit goed, omdat deze de entiteit kan vinden in een nieuw voor beeld utterance. Als u voor beeld uitingen toevoegt en de entiteit niet correct is voor speld, labelt u de entiteit en de subonderdelen. Als de entiteit correct wordt voor speld, moet u de voor spellingen bevestigen.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Een vooraf samengesteld nummer toevoegen om gegevens te extra heren

De order informatie moet ook het aantal artikelen in de volg orde bevatten, zoals het aantal pizzas. Als u deze gegevens wilt extra heren, moet er een nieuw apparaat wordt toegevoegd aan `Order` en dat onderdeel een beperking van een vooraf samengesteld nummer nodig heeft. Door de entiteit te beperken tot een vooraf samengesteld nummer, wordt door de entiteit getallen gevonden en opgehaald, ongeacht of de tekst een cijfer `2`,, of tekst `two`is.

Begin door de vooraf samengestelde nummer entiteit toe te voegen aan de app.

1. Selecteer **entiteiten** in het menu links en selecteer vervolgens een **vooraf samengestelde entiteit toevoegen**.

1. Zoek in het vak **vooraf gemaakte entiteiten toevoegen** naar en selecteer **nummer** en selecteer vervolgens **gereed**.

    ![Vooraf samengestelde entiteit toevoegen](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    De vooraf samengestelde entiteit wordt toegevoegd aan de app, maar is nog geen beperking.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Een subcomponent entiteit met een beperking maken om gegevens te extra heren

De `Order` entiteit moet een `Quantity` subonderdeel hebben om te bepalen hoeveel van een item in de volg orde is. De hoeveelheid moet worden beperkt tot een getal zodat de geëxtraheerde gegevens onmiddellijk beschikbaar zijn voor de client toepassing op naam.

Een beperking wordt toegepast als tekst overeenkomst, hetzij met exacte overeenkomst (zoals een lijst entiteit) of via reguliere expressies (zoals een reguliere expressie-entiteit of een vooraf gedefinieerde entiteit).

Door gebruik te maken van een beperking wordt alleen tekst die overeenkomt met die beperking geëxtraheerd.

1. Selecteer **entiteiten** en selecteer vervolgens `Order` de entiteit.
1. Selecteer **+ onderdeel toevoegen** en geef vervolgens de `Quantity` naam op en selecteer vervolgens ENTER om het nieuwe subonderdeel `Order` toe te voegen aan de entiteit.
1. Selecteer in de **Geavanceerde opties**na de melding voor de geslaagde taak de beperkings potlood.
1. Selecteer in de vervolg keuzelijst het vooraf gemaakte nummer.

    ![Hoeveelheids entiteit maken met vooraf gebouwd nummer als beperking.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    De `Quantity` entiteit wordt toegepast wanneer tekst overeenkomt met de vooraf samengestelde nummer entiteit.

    De entiteit met de beperking wordt gemaakt, maar is nog niet toegepast op het voor beeld uitingen.

    > [!NOTE]
    > Een subonderdeel kan Maxi maal vijf niveaus worden genest in een subonderdeel. Hoewel dit niet wordt weer gegeven in dit artikel, is het beschikbaar via de portal en de API.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Label voorbeeld utterance om LUIS over de entiteit te leren

1. Selecteer **intenties** in de linkernavigatiebalk en selecteer vervolgens de **OrderPizza** intentie. De drie getallen in de volgende uitingen zijn gelabeld, maar zijn visueel onder de `Order` entiteits regel. Dit lagere niveau betekent dat de entiteiten worden gevonden, maar niet als onderdeel van de `Order` entiteit worden beschouwd.

    ![Het vooraf ontwikkelde nummer is gevonden, maar wordt nog niet meegenomen in de order entiteit.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Voorzie de nummers van de `Quantity` entiteit door `2` in het voor beeld utterance `Quantity` te selecteren in de lijst. Label de `6` en `1` in hetzelfde voor beeld utterance.

    ![Label tekst met entiteit hoeveelheid.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>De app trainen om de entiteits wijzigingen toe te passen op de app

Selecteer **trainen** om de app te trainen met deze nieuwe uitingen. Na de training is `Quantity` het subonderdeel correct voor speld in het `Order` onderdeel. Deze juiste voor spelling wordt aangeduid met een ononderbroken lijn.

![Train de app en Bekijk het voor beeld uitingen.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Op dit moment bevat de order enkele details die kunnen worden geëxtraheerd (grootte, hoeveelheid en totale order tekst). De `Order` entiteit is verder verfijnd, zoals pizza toppings, type Crust en side orders. Elk van deze moet worden gemaakt als subonderdelen van de `Order` entiteit.

## <a name="test-the-app-to-validate-the-changes"></a>De app testen om de wijzigingen te valideren

Test de app met behulp van het interactieve **test** paneel. Met dit proces kunt u een nieuwe utterance invoeren en vervolgens de resultaten van de voor spelling weer geven om te zien hoe goed de actieve en getrainde app werkt. De intentie voorspelling moet redelijk zijn verzekerd (meer dan 70%) en de extractie van de entiteit moet ten minste de `Order` entiteit ophalen. De details van de order entiteit ontbreken mogelijk omdat er niet genoeg vijf uitingen zijn om elke case af te handelen.

1. Selecteer **Test** in de bovenste navigatiebalk.
1. Voer de utterance `deliver a medium veggie pizza` in en selecteer ENTER. Het actieve model heeft de juiste intentie gedicteerd met meer dan 70% betrouw baarheid.

    ![Voer een nieuwe utterance in om de intentie te testen.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Selecteer **controleren** om de voor spellingen van de entiteit weer te geven.

    ![Bekijk de voor spellingen van de entiteit in het interactieve test paneel.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    De grootte is correct geïdentificeerd. Houd er rekening mee dat het voor `OrderPizza` beeld uitingen in de intentie geen voor `medium` beeld heeft van een grootte, maar wel een descriptor `SizeDescriptor` van een woordgroepen lijst met het gemiddelde te gebruiken.

    De hoeveelheid is niet juist voor speld. U kunt dit in uw client toepassing oplossen door de grootte standaard te wijzigen in één (1) als er geen grootte wordt geretourneerd in de LUIS-voor spelling.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>De app publiceren voor toegang tot het HTTP-eind punt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Intentie en entiteits voorspelling van het HTTP-eind punt ophalen

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in de adres balk en vervang _YOUR_QUERY_HERE_ door de query die u hebt ingevoerd in het interactieve test paneel.

    `deliver a medium veggie pizza`

    De laatste query string- `query`para meter is, de utterance **query's**.

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
