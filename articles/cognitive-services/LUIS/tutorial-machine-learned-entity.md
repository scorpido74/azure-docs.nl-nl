---
title: 'Zelf studie: gestructureerde gegevens met machine learning-entiteit extra heren-LUIS'
description: Gestructureerde gegevens uit een utterance extra heren met behulp van de machine learning-entiteit. Voeg subentiteiten met functies toe om de nauw keurigheid van de extractie te verg Roten.
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: e16badfb2e024c5d82f1aed8a02aa901dca2c2a9
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85611013"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>Zelf studie: gestructureerde gegevens uit de utterance van de gebruiker met machine learning-entiteiten in Language Understanding extra heren (LUIS)

In deze zelf studie haalt u gestructureerde gegevens op uit een utterance met behulp van de machine learning-entiteit.

De machine learning-entiteit ondersteunt het [model ontledings concept](luis-concept-model.md#v3-authoring-model-decomposition) door subentiteits entiteiten met [functies](luis-concept-feature.md)te leveren.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Machine learning-entiteit toevoegen
> * Subentiteit en functie toevoegen
> * App trainen, testen en publiceren
> * De voor spelling van de entiteit van het eind punt ophalen

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>Waarom een machine learning-entiteit gebruiken?

In deze zelf studie voegt u een machine learning-entiteit toe om gegevens te extra heren uit de utterance van een gebruiker.

De entiteit definieert de gegevens die moeten worden geëxtraheerd uit de utterance. Dit omvat het geven van de gegevens een naam, een type (indien mogelijk), een oplossing van de gegevens als er sprake is van dubbel zinnigheid en de exacte tekst die de gegevens oplevert.

Als u de gegevens wilt definiëren, moet u het volgende doen:
* De entiteit maken
* Label de tekst, in voor beeld uitingen, die de entiteit vertegenwoordigt. Deze labels hebben een voor beeld van LUIS wat de entiteit is en waar deze kan worden gevonden in een utterance.

## <a name="entity-decomposability-is-important"></a>Het desamen stellen van de entiteit is belang rijk

Het afstellen van de entiteit is belang rijk voor zowel de voor spelling als voor gegevens extractie met de entiteit.

Begin met een machine learning-entiteit, die het begin en de entiteit op het hoogste niveau is voor het extra heren van gegevens. Splits de entiteit vervolgens in subentiteiten.

Hoewel u mogelijk niet weet hoe nauw keurig uw entiteit moet zijn wanneer u uw app start, moet een best practice beginnen met een machine learning-entiteit en vervolgens afbreken met subentiteiten als uw app is gerijpt.

In deze zelf studie maakt u een machine learning-entiteit die een bestelling voor een pizza-app vertegenwoordigt. De entiteit haalt tekst op die betrekking heeft op de order, de uitname van de grootte en de hoeveelheid.

Een utterance van `Please deliver one large cheese pizza to me` moet `one large cheese pizza` worden geëxtraheerd als de order, en kan ook worden geëxtraheerd `1` voor hoeveelheid en `large` voor grootte.

## <a name="download-json-file-for-app"></a>JSON-bestand voor de app downloaden

Down load en sla het [JSON-bestand](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)van de app op.

## <a name="import-json-file-for-app"></a>JSON-bestand voor app importeren

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>Door machine geleerde entiteit maken

Als u details over een pizza order wilt extra heren, maakt u een hoofd niveau, een machine learning- `Order` entiteit.

1. Selecteer op de pagina **intenties** de **OrderPizza** intentie.

1. Selecteer in de lijst voor beeld uitingen de volgende utterance.

    |Voor beeld van een bestelling utterance|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Begin met het selecteren van net vóór de meest linkse tekst van `pickup` (#1) en ga vervolgens alleen verder dan de juiste tekst, `anchovies` (#2: Hiermee wordt het label proces beëindigd). Er wordt een pop-upmenu weer gegeven. Voer in het pop-upvenster de naam van de entiteit in `Order` (#3). Selecteer vervolgens `Order Create new entity` in de lijst (#4).

    ![Label begint en eindigt op tekst voor volledige volg orde](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Een entiteit is niet altijd de hele utterance. In dit specifieke geval `pickup` geeft aan hoe de bestelling moet worden ontvangen. Van een conceptueel perspectief `pickup` moeten deel uitmaken van de gelabelde entiteit voor de order.

1. Selecteer in het vak **een entiteits type kiezen** de optie **structuur toevoegen** en selecteer vervolgens **volgende**. Structuur is nodig om subentiteiten, zoals grootte en hoeveelheid, toe te voegen.

    ![Structuur toevoegen aan entiteit](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Selecteer in het vak **subentiteiten toevoegen (optioneel)** **+** de `Order` rij, vervolgens toevoegen `Size` en `Quantity` als subentiteiten, en selecteer vervolgens **maken**.

    > [!div class="mx-imgBorder"]
    > ![Structuur toevoegen aan entiteit](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>Subentiteiten bewerken om de extractie te verbeteren

In de vorige stappen maakt u de entiteit en de subentiteit. Voeg functies toe aan de subentiteiten om de extractie te verbeteren.

### <a name="improve-size-extraction-with-phrase-list"></a>Grootte extractie verbeteren met woordgroepen lijst

1. Selecteer **entiteiten** in het menu links en selecteer vervolgens **order** entiteit.

1. Selecteer op het tabblad **schema en functies** de **grootte** subentiteit en selecteer **+ functie toevoegen**.

1. Selecteer **nieuwe woordgroepen lijst maken** in de vervolg keuzelijst.

1. Voer in het vak **nieuwe woordgroepen lijst maken** de naam in en `SizePhraselist` Voer vervolgens waarden in: `small` , `medium` en `large` . Wanneer in het vak **suggesties** wordt ingevuld, selecteert u `extra large` en `xl` . Selecteer **maken** om de nieuwe woordgroepen lijst te maken.

    Met deze woordgroepen lijst functie kan de `Size` subentiteit woorden vinden die gerelateerd zijn aan de grootte door bijvoorbeeld woorden op te geven. Deze woordgroepen lijst hoeft niet elk woord grootte op te geven, maar moet woorden bevatten die worden verwacht om de grootte aan te duiden.

### <a name="add-sizelist-entity"></a>SizeList-entiteit toevoegen

Bij het toevoegen van een lijst met bekende grootten die door de client toepassing wordt herkend, kunt u ook extra heren.

1. Selecteer **entiteiten** in het menu links en selecteer **+ maken**.

1. Stel de naam van de entiteit zo in dat `SizeListentity` deze gemakkelijk te identificeren is in vergelijking met de `SizePhraselist` in de vorige sectie gemaakte.

1. Voeg de formaten toe die de client toepassing verwacht: `Small` , `Medium` , `Large` , en `XLarge` Voeg synoniemen voor elk toe. De synoniemen moeten de voor waarden zijn die een gebruiker invoert in de chat-bot. De entiteit wordt geëxtraheerd met een lijst entiteit, wanneer deze precies overeenkomt met de genormaliseerde waarde of synoniemen.

    |Genormaliseerde waarde|Synoniemen|
    |--|--|
    |Klein|SM, SML, tiny, kleinste|
    |Normaal|MD, MDM, normaal, gemiddeld, Midden|
    |Groot|LG, LRG, Big|
    |XLarge|XL, grootste, gigantisch|


    > [!div class="mx-imgBorder"]
    > ![Structuur toevoegen aan entiteit](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>Functie van SizeList-entiteit toevoegen

1. Selecteer **entiteiten** in het menu links om terug te keren naar de lijst met entiteiten.

1. Selecteer **volg orde** in de lijst met entiteiten.

1. Selecteer op het tabblad **schema en functies** de entiteit **grootte** en selecteer vervolgens **+ functie toevoegen**.

1. Selecteer **@ SizeListentity** in de vervolg keuzelijst.

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>Vooraf ingebouwde nummer entiteit toevoegen

Het toevoegen van een vooraf gedefinieerde nummer entiteit helpt ook extra heren.

1. Selecteer **entiteiten** in het menu links en selecteer vervolgens **vooraf samengestelde entiteit toevoegen**.

1. Selecteer **getal** in de lijst en selecteer vervolgens **gereed**.

1. Selecteer **entiteiten** in het menu links om terug te keren naar de lijst met entiteiten.

### <a name="add-feature-of-prebuilt-number-entity"></a>Functie van vooraf ingebouwde nummer entiteit toevoegen

1. Selecteer **volg orde** in de lijst met entiteiten.

1. Selecteer op het tabblad **schema en functies** de entiteit **hoeveelheid** en selecteer **+ functie toevoegen**.

1. Selecteer **@ getal** in de vervolg keuzelijst.

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>Vereiste onderdelen configureren

Selecteer op de pagina Details van entiteit voor **order** entiteit het sterretje, `*` voor zowel de functie **@ SizeList** als de functie **@ Number** . Het sterretje wordt weer gegeven in hetzelfde label als de functie naam.

> [!div class="mx-imgBorder"]
> ![Structuur toevoegen aan entiteit](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>Voor beeld van label uitingen

De door de machine geleerde entiteit wordt gemaakt en de subentiteiten beschikken over functies. Voor het volt ooien van de extractie verbetering moet het voor beeld uitingen zijn gelabeld met de subentiteiten.

1. Selecteer **intenties** in het linkernavigatievenster en selecteer vervolgens de **OrderPizza** intentie.

1. Als u het **entiteits palet**wilt openen, selecteert u het **@** symbool in de contextuele werk balk.

1. Selecteer elke entiteit rij in het palet en gebruik vervolgens de palet cursor om de entiteit te selecteren in elk voor beeld utterance. Wanneer u klaar bent, ziet de lijst met entiteiten eruit als in de volgende afbeelding.

    > [!div class="mx-imgBorder"]
    > ![Gedeeltelijke scherm opname van de functie vereist configureren](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>De app trainen

Als u de app wilt trainen, selecteert u **trainen**. Training past de wijzigingen, zoals de nieuwe entiteiten en het gelabelde uitingen, toe op het actieve model.

## <a name="add-a-new-example-utterance"></a>Een nieuw voor beeld utterance toevoegen

1. Voeg na de training een nieuw voor beeld utterance toe aan de `OrderPizza` bedoeling om te zien hoe goed Luis de machine learning-entiteit begrijpt.

    |Voor beeld van een bestelling utterance|
    |--|
    |`I need a large pepperoni pizza`|

    De algemene bovenste entiteit, `Order` heeft het label en de `Size` subentiteit wordt ook gelabeld met stippel lijnen.

    > [!div class="mx-imgBorder"]
    > ![Gedeeltelijke scherm opname van nieuw voor beeld utterance voorspeld met entiteit](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    De stippel lijn geeft de voor spelling aan op basis van de huidige getrainde app.

1. Als u de voor spelling in een entiteit met een label wilt wijzigen, selecteert u het vinkje op dezelfde rij.

    > [!div class="mx-imgBorder"]
    > ![Gedeeltelijke scherm opname van nieuw voor beeld utterance voorspeld met entiteit](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    Op dit moment werkt de machine learning-entiteit samen, omdat deze de entiteit kan vinden in een nieuw voor beeld utterance. Als u voor beeld uitingen toevoegt en de entiteit niet correct is voor speld, labelt u de entiteit en de subentiteiten. Als de entiteit correct wordt voor speld, moet u de voor spellingen bevestigen.


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>De app trainen om de entiteits wijzigingen toe te passen op de app

Selecteer **trainen** om de app te trainen met deze nieuwe utterance.

Op dit moment bevat de order enkele details die kunnen worden geëxtraheerd (grootte, hoeveelheid en totale order tekst). De entiteit is verder verfijnd `Order` , zoals pizza toppings, type Crust en side orders. Elk van deze moet worden gemaakt als subentiteiten van de `Order` entiteit.

## <a name="test-the-app-to-validate-the-changes"></a>De app testen om de wijzigingen te valideren

Test de app met behulp van het interactieve **test** paneel. Met dit proces kunt u een nieuwe utterance invoeren en vervolgens de resultaten van de voor spelling weer geven om te zien hoe goed de actieve, getrainde app werkt. De intentie voorspelling moet redelijk zijn verzekerd (meer dan 60%) en de extractie van de entiteit moet ten minste de `Order` entiteit ophalen. De details van de order entiteit ontbreken mogelijk omdat deze enkele uitingen niet voldoende zijn om elke case af te handelen.

1. Selecteer **Test** in de bovenste navigatiebalk.
1. Voer de utterance in `2 small cheese pizzas for pickup` en selecteer ENTER. Het actieve model heeft de juiste intentie gedicteerd met meer dan 60% betrouw baarheid.


1. Selecteer **controleren** om de voor spellingen van de entiteit weer te geven.

    > [!div class="mx-imgBorder"]
    > ![Gedeeltelijke scherm opname van de voor spellingen van de entiteit in het interactieve test paneel.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>De app publiceren voor toegang tot het HTTP-eind punt

Om een voorspelling van LUIS te ontvangen in een chatbot of een andere clienttoepassing, moet u de app publiceren naar het eindpunt.

1. Selecteer in de navigatie rechtsboven op **publiceren** .

    ![Scherm opname van de knop LUIS publiceren naar eind punt in het bovenste menu rechts](./media/howto-publish/publish-button.png)

1. Selecteer de **productie** sleuf, selecteer **instellingen wijzigen**, selecteer **sentimentanalyse**en selecteer vervolgens **gereed**.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van LUIS publiceren naar eind punt](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. Selecteer de koppeling **uw eind punt-Url's openen** in de melding om naar de pagina **Azure-resources** te gaan. De eind punt-Url's worden weer gegeven als de **voorbeeld query**.

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Intentie en entiteits voorspelling van het HTTP-eind punt ophalen

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in de adres balk en vervang _YOUR_QUERY_HERE_ door de query die u hebt ingevoerd in het interactieve test paneel.

    `2 small cheese pizzas for pickup`

    De laatste query string-para meter is `query` , de utterance **query's**.

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
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
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
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

In deze zelf studie gebruikt de app een machine learning-entiteit om het doel van de utterance van een gebruiker te vinden en gegevens uit die utterance te extra heren. Met de machine learning-entiteit kunt u de details van de entiteit afbreken.

> [!div class="nextstepaction"]
> [Een vooraf gemaakte entiteit KeyPhrase toevoegen](luis-quickstart-intent-and-key-phrase.md)
