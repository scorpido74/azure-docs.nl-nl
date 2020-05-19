---
title: 'Zelf studie: gestructureerde gegevens ophalen met door de machine geleerde entiteit-LUIS'
description: Gestructureerde gegevens uit een utterance extra heren met behulp van de door de machine geleerde entiteit. Voeg subentiteiten met functies toe om de nauw keurigheid van de extractie te verg Roten.
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: d1bc8fc6aac52e264cb4352ca05f9df45ccfc50e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588867"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Zelf studie: gestructureerde gegevens ophalen van gebruikers utterance met entiteiten die door machines zijn geleerd in Language Understanding (LUIS)

In deze zelf studie extraheert u gestructureerde gegevens uit een utterance met behulp van de door de machine geleerde entiteit.

De door de machine geleerde entiteit ondersteunt het [model ontledings concept](luis-concept-model.md#v3-authoring-model-decomposition) door subentiteits entiteiten met [functies](luis-concept-feature.md)te leveren.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Door machine geleerde entiteit toevoegen
> * Subentiteit en functie toevoegen
> * App trainen, testen en publiceren
> * De voor spelling van de entiteit van het eind punt ophalen

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Waarom een door de machine geleerde entiteit gebruiken?

In deze zelf studie wordt een door een machine geleerde entiteit toegevoegd voor het ophalen van gegevens uit de utterance van een gebruiker.

De entiteit definieert de gegevens die moeten worden geëxtraheerd uit de utterance. Dit omvat het geven van de gegevens een naam, een type (indien mogelijk), een oplossing van de gegevens als er sprake is van dubbel zinnigheid en de exacte tekst die de gegevens oplevert.

Als u de gegevens wilt definiëren, moet u het volgende doen:
* De entiteit maken
* Label de tekst, in voor beeld uitingen, die de entiteit vertegenwoordigt. Deze labels hebben een voor beeld van LUIS wat de entiteit is en waar deze kan worden gevonden in een utterance.

## <a name="entity-decomposability-is-important"></a>Het desamen stellen van de entiteit is belang rijk

Het afstellen van de entiteit is belang rijk voor zowel de voor spelling als voor gegevens extractie met de entiteit.

Begin met een door de machine geleerde entiteit, die de begin-en de entiteit op het hoogste niveau is voor het uitpakken van gegevens. Splits de entiteit vervolgens in subentiteiten.

Hoewel u mogelijk niet weet hoe nauw keurig uw entiteit moet zijn wanneer u uw app start, moet een best practice beginnen met een door de machine geleerde entiteit en vervolgens afbreken met subentiteiten als uw app is gerijpt.

In deze zelf studie maakt u een door de machine geleerde entiteit die een bestelling voor een pizza-app vertegenwoordigt. De entiteit haalt tekst op die betrekking heeft op de order, de uitname van de grootte en de hoeveelheid.

Een utterance van `Please deliver one large cheese pizza to me` moet `one large cheese pizza` worden geëxtraheerd als de order, en kan ook worden geëxtraheerd `1` voor hoeveelheid en `large` voor grootte.

## <a name="download-json-file-for-app"></a>JSON-bestand voor de app downloaden

Down load en sla het [JSON-bestand](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)van de app op.

## <a name="import-json-file-for-app"></a>JSON-bestand voor app importeren

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Tekst labelen als entiteiten in voor beeld uitingen

Als u details over een pizza order wilt extra heren, maakt u een op het hoogste niveau, door de machine geleerde `Order` entiteit.

1. Selecteer op de pagina **intenties** de **OrderPizza** intentie.

1. Selecteer in de lijst voor beeld uitingen de volgende utterance.

    |Voor beeld van een bestelling utterance|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Begin met het selecteren van net vóór de meest linkse tekst van `pickup` (#1) en ga vervolgens alleen verder dan de juiste tekst, `anchovies` (#2: Hiermee wordt het label proces beëindigd). Er wordt een pop-upmenu weer gegeven. Voer in het pop-upvenster de naam van de entiteit in `Order` (#3). Selecteer vervolgens `Order - Create new entity` in de lijst (#4).

    ![Label begint en eindigt op tekst voor volledige volg orde](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Een entiteit is niet altijd de hele utterance. In dit specifieke geval `pickup` geeft aan hoe de bestelling moet worden ontvangen. Van een conceptueel perspectief `pickup` moeten deel uitmaken van de gelabelde entiteit voor de order.

1. Selecteer in het vak **een entiteits type kiezen** de optie **structuur toevoegen** en selecteer vervolgens **volgende**. Structuur is nodig om subentiteiten, zoals grootte en hoeveelheid, toe te voegen.

    ![Structuur toevoegen aan entiteit](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. In het vak **een door een machine geleerde entiteit maken** , in het vak **structuur** , `Size` selecteert u vervolgens ENTER.
1. Als u een **functie**wilt toevoegen, selecteert u de `+` in het gedeelte **onderdelen** en selecteert u **nieuwe woordgroepen lijst maken**.

1. Voer in het vak **nieuwe woordgroepen lijst maken** de naam in en `SizeFeature` Voer vervolgens waarden in: `small` , `medium` en `large` . Wanneer in het vak **suggesties** wordt ingevuld, selecteert u `extra large` en `xl` . Selecteer **gereed** om de nieuwe woordgroepen lijst te maken.

    Met deze woordgroepen lijst functie kan de `Size` subentiteit woorden vinden die gerelateerd zijn aan de grootte door bijvoorbeeld woorden op te geven. In deze lijst hoeft u niet elk woord grootte op te geven, maar moet woorden bevatten die worden verwacht om de grootte aan te duiden.

    ![Een functie maken voor de subentiteit grootte](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Selecteer in het venster **een door een machine geleerde entiteit maken** de optie **maken** om het maken van de subentiteit te volt ooien `Size` .

    De `Order` entiteit met een `Size` entiteit wordt gemaakt, maar alleen de `Order` entiteit is toegepast op de utterance. U moet de `Size` entiteits tekst labelen in het voor beeld utterance.

1. In hetzelfde voor beeld utterance labelt u de subentiteit **grootte** van `large` door het woord te selecteren en vervolgens de entiteit **grootte** te selecteren in de vervolg keuzelijst.

    ![Label de entiteit grootte voor tekst in het utterance.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    De lijn wordt onder de tekst weer, omdat het labelen en de voor spelling overeenkomen omdat u de tekst _expliciet_ labelt.

1. Label de `Order` entiteit in de resterende uitingen samen met de grootte-entiteit. De vier Kante haken in de tekst geven de gelabelde `Order` entiteit en de `Size` entiteit binnen.

    |Voor beeld van een bestelling uitingen|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Maak de entiteiten en subentiteiten in alle resterende voor beeld-uitingen.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Hoe behandel ik geïmpliceerde gegevens zoals de letter die `a` een enkele pizza impliceert? Of het ontbreken van `pickup` en `delivery` om aan te geven waar de pizza wordt verwacht? Of het ontbreken van een grootte om de standaard grootte van klein of groot te geven? Overweeg impliciete verwerking van gegevens te behandelen als onderdeel van uw bedrijfs regels in de client toepassing in plaats van of naast LUIS.

1. Als u de app wilt trainen, selecteert u **trainen**. Training past de wijzigingen, zoals de nieuwe entiteiten en het gelabelde uitingen, toe op het actieve model.

1. Voeg na de training een nieuw voor beeld-utterance toe aan het doel om te zien hoe goed LUIS de door de machine geleerde entiteit begrijpt.

    |Voor beeld van een bestelling utterance|
    |--|
    |`pickup XL meat lovers pizza`|

    De algemene bovenste entiteit, `Order` heeft het label en de `Size` subentiteit wordt ook gelabeld met stippel lijnen.

    ![Nieuw voor beeld utterance voorspeld met entiteit](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    De stippel lijn geeft de voor spelling aan.

1. Als u de voor spelling wilt wijzigen in een entiteit met een label, selecteert u de rij en selecteert u voor **spellingen van entiteit bevestigen**.

    ![Accepteer de voor spelling door entiteits voorspelling bevestigen te selecteren.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Op dit moment werkt de door de machine geleerde entiteit goed, omdat deze de entiteit kan vinden in een nieuw voor beeld utterance. Als u voor beeld uitingen toevoegt en de entiteit niet correct is voor speld, labelt u de entiteit en de subentiteiten. Als de entiteit correct wordt voor speld, moet u de voor spellingen bevestigen.


<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="add-subentity-with-feature-of-prebuilt-entity"></a>Subentiteit toevoegen met functie van vooraf gebouwde entiteit

De order informatie moet ook het aantal items in de volg orde bevatten, zoals het aantal pizzas. Als u deze gegevens wilt extra heren, moet er een nieuwe door de machine geleerde subentiteit worden toegevoegd aan `Order` en die subentiteit moet een vereiste functie van een vooraf samengesteld nummer hebben. Door gebruik te maken van een functie van een vooraf gedefinieerde entiteit, de entiteit op een vooraf gebouwd nummer, wordt door de entiteit getallen gevonden en opgehaald, ongeacht of de tekst een cijfer, `2` , of tekst is `two` .

## <a name="add-prebuilt-number-entity-to-app"></a>Vooraf gedefinieerde cijferentiteit aan app toevoegen
De order gegevens moeten ook het aantal items in de volg orde hebben, zoals het aantal pizzas. Als u deze gegevens wilt extra heren, moet er een nieuw apparaat worden toegevoegd aan `Order` en dat onderdeel een vereiste functie van een vooraf gebouwd nummer nodig heeft. Door de entiteit te beperken tot een vooraf samengesteld nummer, wordt door de entiteit getallen gevonden en opgehaald, ongeacht of de tekst een cijfer, `2` , of tekst is `two` .

Begin door de vooraf samengestelde nummer entiteit toe te voegen aan de app.

1. Selecteer **entiteiten** in het menu links en selecteer vervolgens een **vooraf samengestelde entiteit toevoegen**.

1. Zoek in het vak **vooraf gemaakte entiteiten toevoegen** naar en selecteer **nummer** en selecteer vervolgens **gereed**.

    ![Vooraf samengestelde entiteit toevoegen](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    De vooraf samengestelde entiteit wordt toegevoegd aan de app, maar is nog geen functie.

## <a name="create-subentity-entity-with-required-feature-to-help-extract-data"></a>Entiteit voor subentiteit maken met vereiste functie om gegevens te extra heren

De `Order` entiteit moet een `Quantity` subentiteit hebben om te bepalen hoeveel van een item in de volg orde is. De hoeveelheid moet een vereiste functie van een vooraf gebouwd nummer gebruiken, zodat de geëxtraheerde gegevens onmiddellijk beschikbaar zijn voor de client toepassing op naam.

Een vereiste functie wordt als tekst overeenkomst toegepast, hetzij met exacte overeenkomst (zoals een lijst entiteit) of via reguliere expressies (zoals een reguliere expressie-entiteit of een vooraf gedefinieerde entiteit).

Door gebruik te maken van een niet door machines geleerde entiteit als functie, wordt alleen tekst die overeenkomt, geëxtraheerd.

1. Selecteer **entiteiten** en selecteer vervolgens de `Order` entiteit.
1. Selecteer **+ entiteit toevoegen** en geef vervolgens de naam `Quantity` op en selecteer vervolgens ENTER om de nieuwe subentiteit toe te voegen aan de `Order` entiteit.
1. Selecteer in de **Geavanceerde opties**na de melding voor de geslaagde taak de beperkings potlood.
1. Selecteer in de vervolg keuzelijst het vooraf gemaakte nummer.

    ![Hoeveelheids entiteit maken met vooraf gebouwd nummer als beperking.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    De `Quantity` entiteit wordt toegepast wanneer tekst overeenkomt met de vooraf samengestelde nummer entiteit.

    De entiteit met de vereiste functie wordt gemaakt, maar is nog niet toegepast op het voor beeld uitingen.

    > [!NOTE]
    > Een subentiteit kan worden genest in een subentiteit van Maxi maal vijf niveaus. Hoewel dit niet wordt weer gegeven in dit artikel, is het beschikbaar via de portal en de API.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Label voorbeeld utterance om LUIS over de entiteit te leren

1. Selecteer **intenties** in de linkernavigatiebalk en selecteer vervolgens de **OrderPizza** intentie. De drie getallen in de volgende uitingen zijn gelabeld, maar zijn visueel onder de `Order` entiteits regel. Dit lagere niveau betekent dat de entiteiten worden gevonden, maar niet als onderdeel van de `Order` entiteit worden beschouwd.

    ![Het vooraf ontwikkelde nummer is gevonden, maar wordt nog niet meegenomen in de order entiteit.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Voorzie de nummers van de `Quantity` entiteit door `2` in het voor beeld utterance te selecteren in de `Quantity` lijst. Label de `6` en `1` in hetzelfde voor beeld utterance.

    ![Label tekst met entiteit hoeveelheid.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>De app trainen om de entiteits wijzigingen toe te passen op de app

Selecteer **trainen** om de app te trainen met deze nieuwe uitingen. Na de training `Quantity` is de subentiteit correct voor speld in de `Order` entiteit. Deze juiste voor spelling wordt aangeduid met een ononderbroken lijn.

![Train de app en Bekijk het voor beeld uitingen.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Op dit moment bevat de order enkele details die kunnen worden geëxtraheerd (grootte, hoeveelheid en totale order tekst). De entiteit is verder verfijnd `Order` , zoals pizza toppings, type Crust en side orders. Elk van deze moet worden gemaakt als subentiteiten van de `Order` entiteit.

## <a name="test-the-app-to-validate-the-changes"></a>De app testen om de wijzigingen te valideren

Test de app met behulp van het interactieve **test** paneel. Met dit proces kunt u een nieuwe utterance invoeren en vervolgens de resultaten van de voor spelling weer geven om te zien hoe goed de actieve en getrainde app werkt. De intentie voorspelling moet redelijk zijn verzekerd (meer dan 70%) en de extractie van de entiteit moet ten minste de `Order` entiteit ophalen. De details van de order entiteit ontbreken mogelijk omdat er niet genoeg vijf uitingen zijn om elke case af te handelen.

1. Selecteer **Test** in de bovenste navigatiebalk.
1. Voer de utterance in `deliver a medium veggie pizza` en selecteer ENTER. Het actieve model heeft de juiste intentie gedicteerd met meer dan 70% betrouw baarheid.

    ![Voer een nieuwe utterance in om de intentie te testen.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Selecteer **controleren** om de voor spellingen van de entiteit weer te geven.

    ![Bekijk de voor spellingen van de entiteit in het interactieve test paneel.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    De grootte is correct geïdentificeerd. Houd er rekening mee dat het voor beeld uitingen in de `OrderPizza` intentie geen voor beeld heeft van een `medium` grootte, maar wel een functie van een `SizeFeature` woordgroepen lijst bevat waarin medium is opgenomen.

    De hoeveelheid is niet juist voor speld. U kunt dit in uw client toepassing oplossen door de grootte standaard te wijzigen in één (1) als er geen grootte wordt geretourneerd in de LUIS-voor spelling.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>De app publiceren voor toegang tot het HTTP-eind punt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Intentie en entiteits voorspelling van het HTTP-eind punt ophalen

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in de adres balk en vervang _YOUR_QUERY_HERE_ door de query die u hebt ingevoerd in het interactieve test paneel.

    `deliver a medium veggie pizza`

    De laatste query string-para meter is `query` , de utterance **query's**.

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
