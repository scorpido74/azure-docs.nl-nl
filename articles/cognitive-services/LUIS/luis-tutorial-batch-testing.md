---
title: 'Zelfstudie: Batchgewijs testen om problemen op te sporen: LUIS'
description: In deze zelfstudie wordt gedemonstreerd hoe u met batchgewijze testen de kwaliteit van uw Language Understanding-app (LUIS) kunt valideren.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: 7dd181f8cd398dd683296b446028b398a9800b53
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298308"
---
# <a name="tutorial-batch-test-data-sets"></a>Zelfstudie: Gegevenssets batchgewijs testen

In deze zelfstudie wordt gedemonstreerd hoe u met batchgewijze testen de kwaliteit van uw Language Understanding-app (LUIS) kunt valideren.

Met batchgewijze tests kunt u de actieve status van het getrainde model valideren met behulp van een bekende set gelabelde uitingen en entiteiten. Voeg de uitingen toe aan het batchbestand in JSON-indeling en stel de entiteitslabels in die u in de uiting wilt voorspellen.

Vereisten voor batchgewijs testen:

* Maximaal 1000 uitingen per test.
* Geen duplicaten.
* Toegestane entiteitstypen: alleen ML-entiteiten.

Als u een andere app dan deze zelfstudie gebruikt, moet u *niet* de voorbeelduitingen gebruiken die al aan uw app zijn toegevoegd.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Een batchtestbestand maken
> * Een batchtest uitvoeren
> * Testresultaten evalueren

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Voorbeeld-app importeren

Importeer een app voor het opnemen van een pizzabestelling, zoals `1 pepperoni pizza on thin crust`.

1.  Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true) en sla het op.

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Importeer de JSON in een nieuwe app met de naam `Pizza app`.


1. Selecteer **Trainen** rechtsboven in de navigatiebalk om de app te trainen.

## <a name="what-should-the-batch-file-utterances-include"></a>Welke uitingen moeten in het batchbestand worden opgenomen

Het batchbestand moet uitingen bevatten met gelabelde machine learning-entiteiten op het hoogste niveau, met inbegrip van de begin- en eindpositie. De uitingen mogen geen deel uitmaken van de voorbeelden die al in de app staan. Het moeten uitingen zijn die u positief wilt voorspellen voor de intentie en entiteiten.

U kunt afzonderlijke tests uitvoeren op intentie en/of entiteit of alle tests (maximaal 1000 uitingen) in hetzelfde bestand hebben.

## <a name="batch-file"></a>Batchbestand

Het JSON-voorbeeldbestand bevat een uiting met een gelabelde entiteit om u een idee te geven hoe een testbestand eruitziet. Uw eigen tests moeten veel uitingen met de juiste intentie en gelabelde machine learning-entiteit bevatten.

1. Maak `pizza-with-machine-learned-entity-test.json` in een teksteditor of [download](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) het.

2. Voeg aan het batchbestand in JSON-indeling een uiting toe met de **intentie** die moet worden voorspeld in de test.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>De batch uitvoeren

1. Selecteer **Test** in de bovenste navigatiebalk.

2. Selecteer het **paneel Batchgewijs testen** in het paneel aan de rechterkant.

3. Selecteer **Gegevensset importeren**.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van LUIS-app met Gegevensset importeren gemarkeerd](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Kies de bestandslocatie van het bestand `pizza-with-machine-learned-entity-test.json`.

5. Geef de gegevensset de naam `pizza test` en selecteer **Gereed**.

    > [!div class="mx-imgBorder"]
    > ![Bestand selecteren](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Selecteer de knop **Run** (Uitvoeren).

7. Selecteer **Resultaten weergeven**.

8. Evalueer de resultaten in de grafiek en legenda.

## <a name="review-batch-results-for-intents"></a>Batchresultaten voor intenties evalueren

In de testresultaten wordt grafisch weergegeven hoe de testuitingen zijn voorspeld voor de actieve versie.

In de batchgrafiek worden vier kwadranten met resultaten weergegeven. Rechts van de grafiek ziet u een filter. Het filter bevat intenties en entiteiten. Wanneer u een [sectie van de grafiek](luis-concept-batch-test.md#batch-test-results) of een punt in de grafiek selecteert, worden de gekoppelde uitingen weergegeven onder de grafiek.

Bij het aanwijzen van de grafiek met de muisaanwijzer kunt u de weergave in de grafiek vergroten of verkleinen met een muiswiel. Dit is handig wanneer veel punten in het diagram zich dicht op elkaar bevinden.

De grafiek is verdeeld in vier kwadranten, waarbij twee van de secties rood worden weergegeven.

1. Selecteer de intentie **ModifyOrder** in de filterlijst.

    > [!div class="mx-imgBorder"]
    > ![De intentie ModifyOrder selecteren in de filterlijst](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    De uiting wordt voorspeld als een **terecht-positief**, wat betekent dat de uiting correct is gematcht met de positieve voorspelling die in het batchbestand is vermeld.

    > [!div class="mx-imgBorder"]
    > ![Uiting is correct gematcht met de betreffende positieve voorspelling](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Het groene vinkje in de filterlijst geeft ook aan dat de test voor elke intentie is geslaagd. Alle andere intenties worden weergegeven met een positieve score van 1/1, omdat de uiting is getest op basis van elke intentie, als een negatieve test voor alle intenties die niet worden vermeld in de batchtest.

1. Selecteer de intentie **Bevestiging**. Deze intentie wordt niet vermeld in de batchtest. Dit is dus een negatieve test van de uiting die wordt vermeld in de batchtest.

    > [!div class="mx-imgBorder"]
    > ![Uiting is correct negatief voorspeld voor niet-vermelde intentie in batchbestand](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    De negatieve test is geslaagd, zoals aangegeven met de groene tekst in het filter, en het raster.

## <a name="review-batch-test-results-for-entities"></a>Batchtestresultaten voor entiteiten evalueren

De entiteit ModifyOrder, een machine learning-entiteit met subentiteiten, geeft weer of de entiteit op het hoogste niveau overeenkomt en hoe de subentiteiten worden voorspeld.

1. Selecteer de entiteit **ModifyOrder** in de filterlijst en selecteer vervolgens de cirkel in het raster.

1. De voorspelling van de entiteit wordt onder de grafiek weergegeven. De weergave bevat ononderbroken lijnen voor voorspellingen die overeenkomen met de verwachting en stippellijnen voor voorspellingen die niet overeenkomen met de verwachting.

    > [!div class="mx-imgBorder"]
    > ![Bovenliggende entiteit is correct voorspeld in batchbestand](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Fouten opsporen met een batchtest

In deze zelfstudie hebt u geleerd hoe u een test uitvoert en de resultaten interpreteert. Hierbij is niet besproken hoe u moet omgaan met mislukte testresultaten.

* Zorg ervoor dat u zowel positieve als negatieve uitingen opneemt in uw test, met inbegrip van uitingen die kunnen zijn voorspeld voor een andere, maar wel gerelateerde intentie.
* Voor mislukte uitingen moet u de volgende taken uitvoeren en vervolgens de tests opnieuw uitvoeren:
    * De huidige voorbeelden voor intenties en entiteiten evalueren en de voorbeelduitingen van de actieve versie controleren op juistheid van labels voor intenties en entiteiten.
    * Kenmerken toevoegen waarmee uw app intenties en entiteiten kan voorspellen
    * Meer positieve voorbeelduitingen toevoegen
    * Verdeling van voorbeelduitingen voor alle intenties controleren

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Volgende stap

In de zelfstudie hebt u een batchtest gebruikt om het huidige model te valideren.

> [!div class="nextstepaction"]
> [Meer informatie over patronen](luis-tutorial-pattern.md)

