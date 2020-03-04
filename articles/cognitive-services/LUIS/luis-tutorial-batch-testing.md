---
title: 'Zelf studie: batch testen om problemen op te sporen-LUIS'
description: In deze zelf studie wordt gedemonstreerd hoe u batch tests kunt gebruiken om de kwaliteit van uw Language Understanding-app (LUIS) te valideren.
ms.topic: tutorial
ms.date: 03/02/2020
ms.openlocfilehash: c276f0b52f83937fbe3b6fd9e0b7c1a66f665095
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250471"
---
# <a name="tutorial-batch-test-data-sets"></a>Zelf studie: gegevens sets batch testen

In deze zelf studie wordt gedemonstreerd hoe u batch tests kunt gebruiken om de kwaliteit van uw Language Understanding-app (LUIS) te valideren.

Batch testen, kunt u voor het valideren van de actieve status van het model met een bekende set gelabelde uitingen en entiteiten getraind. In de JSON-indeling batch-bestand, de utterances toevoegen en instellen van de entiteit-labels die u nodig hebt binnen de utterance voorspeld.

Vereisten voor het testen van batch:

* Maximaal 1000 uitingen per test.
* Geen dubbele waarden.
* Toegestane entiteits typen: alleen door machines geleerde entiteiten.

Wanneer u een andere app dan deze zelf *studie gebruikt, mag u* de voor beeld-uitingen al aan uw app toevoegen.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Maak een batchbestand testen
> * Een batch-test uitvoeren
> * Bekijk de resultaten

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Voorbeeld-app importeren

Een app importeren die een pizza-volg orde heeft, zoals `1 pepperoni pizza on thin crust`.

1.  Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true) en sla het op.

1. Gebruik de [Preview-Luis Portal](https://preview.luis.ai/), IMPORTEER de json in een nieuwe app, noem de app `Pizza app`.

1. Selecteer **trainen** in de rechter bovenhoek van de navigatie om de app te trainen.

## <a name="what-should-the-batch-file-utterances-include"></a>Wat moet het batch bestand uitingen bevatten

Het batch-bestand moet uitingen bevatten met entiteiten op het hoogste niveau die door machines zijn geleerd, met inbegrip van de begin-en eind positie. De uitingen mag geen deel uitmaken van de voor beelden die al in de app staan. Ze moeten uitingen zijn om te voors pellen voor intentie en entiteiten.

U kunt testen met behulp van intentie en/of entiteit scheiden of alle tests (Maxi maal 1000 uitingen) in hetzelfde bestand hebben.

## <a name="batch-file"></a>Batch-bestand

De JSON van het voor beeld bevat een utterance met een gelabelde entiteit die illustreert hoe een test bestand eruitziet. In uw eigen tests moet u veel uitingen hebben met de juiste intentie en door de machine geleerde entiteit label.

1. Maak `pizza-with-machine-learned-entity-test.json` in een tekst editor of [down load](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) het bestand.

2. Voeg in het batch bestand met JSON-indeling een utterance toe met de **intentie** die u in de test wilt voors pellen.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Voer de batch

1. Selecteer **test** in de bovenste navigatie balk.

2. Selecteer **batch test paneel** in het deel venster aan de rechter kant.

3. Selecteer **gegevensset importeren**.

    > [!div class="mx-imgBorder"]
    > Scherm opname van LUIS-app ![met import gegevensset gemarkeerd](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Kies de bestands locatie van het `pizza-with-machine-learned-entity-test.json` bestand.

5. Geef de gegevensset een naam `pizza test` en selecteer **gereed**.

    > [!div class="mx-imgBorder"]
    > ![Selecteer bestand](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Selecteer de knop **Run**.

7. Selecteer **resultaten weer geven**.

8. Bekijk de resultaten in de grafiek en legenda.

## <a name="review-batch-results-for-intents"></a>Batch resultaten voor intenties controleren

De test resultaten geven grafisch weer hoe de test uitingen is voor speld op basis van de actieve versie.

De batch worden vier kwadranten van de resultaten weergegeven. Aan de rechterkant van de grafiek is een filter. Het filter bevat intents en entiteiten. Wanneer u een [sectie van de grafiek](luis-concept-batch-test.md#batch-test-results) of een punt in de grafiek selecteert, worden de gekoppelde utterance (s) weer gegeven onder de grafiek.

Bij het aanwijzen van de grafiek, kan muiswiel vergroten of verkleinen van de weergave in de grafiek. Dit is handig wanneer er veel punten op de grafiek geclusterde nauw samen.

De grafiek is in vier kwadranten, met twee van de secties in het rood weergegeven.

1. Selecteer de **ModifyOrder** intentie in de filter lijst.

    > [!div class="mx-imgBorder"]
    > ![Selecteer ModifyOrder intentie uit filter lijst](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    De utterance wordt voor speld als een **echte, positieve** betekenis die de utterance met de positieve voor spelling in het batch-bestand heeft gevonden.

    > [!div class="mx-imgBorder"]
    > ![Utterance overeenkomt met zijn positieve Voorspellings](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Het groene vinkje in de lijst met filters geeft ook het succes van de test voor elk doel aan. Alle andere intenten worden weer gegeven met een 1/1 positieve score, omdat de utterance is getest op basis van elke intentie, als een negatieve test voor de intenties die niet worden vermeld in de batch-test.

1. Selecteer het **bevestigings** doel. Deze intentie wordt niet vermeld in de batch test, zodat dit een negatieve test is van de utterance die wordt vermeld in de batch-test.

    > [!div class="mx-imgBorder"]
    > ![Utterance is voor speld voor een niet-gevermeldde intentie in batch-bestand](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    De negatieve test is geslaagd, zoals vermeld bij de groene tekst in het filter en het raster.

## <a name="review-batch-test-results-for-entities"></a>Batch test resultaten voor entiteiten controleren

De ModifyOrder-entiteit, als een machine-entiteit met subentiteiten, geeft weer of de entiteit op het hoogste niveau overeenkomt en geeft weer hoe de subentiteiten worden voor speld.

1. Selecteer de entiteit **ModifyOrder** in de lijst Filter en selecteer vervolgens de cirkel in het raster.

1. De voor spelling van de entiteit wordt onder de grafiek weer gegeven. De weer gave bevat ononderbroken lijnen voor voor spellingen die overeenkomen met de verwachting en stippel lijnen voor voor spellingen die niet overeenkomen met de verwachting.

    > [!div class="mx-imgBorder"]
    > het bovenliggende item van ![entiteit is voor speld in batch-bestand](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Fouten vinden met een batch test

In deze zelf studie hebt u geleerd hoe u een test uitvoert en de resultaten interpreteert. Het heeft geen betrekking op de test filosofie of hoe kan worden gereageerd op mislukte testen.

* Zorg ervoor dat u zowel positieve als negatieve uitingen in uw test onderneemt, inclusief uitingen die mogelijk voor speld zijn voor een andere, maar verwante intentie.
* Voor een mislukte uitingen voert u de volgende taken uit en voert u de tests opnieuw uit:
    * Bekijk de huidige voor beelden voor intents en entiteiten, Controleer of de voor beeld-uitingen van de actieve versie juist zijn voor intentie en entiteit labeling.
    * Functies toevoegen die uw app kunnen voors pellen en entiteiten
    * Meer positieve voorbeeld uitingen toevoegen
    * Het saldo van voor beeld-uitingen in intenties controleren

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Volgende stap

De zelf studie heeft een batch test gebruikt om het huidige model te valideren.

> [!div class="nextstepaction"]
> [Meer informatie over patronen](luis-tutorial-pattern.md)

