---
title: 'Zelfstudie: Batchtesten om problemen te vinden - LUIS'
description: In deze zelfstudie wordt uitgelegd hoe u batchtesten gebruiken om de kwaliteit van uw LUIS-app (Language Understanding) te valideren.
ms.topic: tutorial
ms.date: 03/02/2020
ms.openlocfilehash: c276f0b52f83937fbe3b6fd9e0b7c1a66f665095
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78250471"
---
# <a name="tutorial-batch-test-data-sets"></a>Zelfstudie: Batchtestgegevenssets

In deze zelfstudie wordt uitgelegd hoe u batchtesten gebruiken om de kwaliteit van uw LUIS-app (Language Understanding) te valideren.

Met batchtests u de status van het actieve, getrainde model valideren met een bekende set gelabelde uitingen en entiteiten. Voeg in het met JSON opgemaakte batchbestand de uitingen toe en stel de entiteitslabels in die u in de utterance wilt voorspellen.

Eisen voor batchtests:

* Maximaal 1000 uitingen per test.
* Geen duplicaten.
* Entiteitstypen toegestaan: alleen bewerkte entiteiten.

Gebruik bij het gebruik van een andere app dan deze zelfstudie de voorbeelduitingen die al aan uw app zijn *toegevoegd, niet.*

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Een batchtestbestand maken
> * Een batchtest uitvoeren
> * Testresultaten bekijken

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Voorbeeld-app importeren

Importeer een app die een `1 pepperoni pizza on thin crust`pizzabestelling aanneemt, zoals .

1.  Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true) en sla het op.

1. Gebruik de [preview LUIS-portal,](https://preview.luis.ai/)importeer de JSON `Pizza app`in een nieuwe app en geef de app een naam.

1. Selecteer **Trainen** in de rechterbovenhoek van de navigatie om de app te trainen.

## <a name="what-should-the-batch-file-utterances-include"></a>Wat moeten de uitingen van het batchbestand bevatten

Het batchbestand moet uitingen bevatten met door de bovenste niveaus aangeleerde entiteiten met het label start- en eindpositie. De uitingen mogen geen deel uitmaken van de voorbeelden die al in de app staan. Het moeten uitingen zijn die u positief wilt voorspellen voor intentie en entiteiten.

U tests op intentie en/of entiteit scheiden of alle tests (maximaal 1000 uitingen) in hetzelfde bestand hebben.

## <a name="batch-file"></a>Batchbestand

Het voorbeeld JSON bevat één utterance met een gelabelde entiteit om te illustreren hoe een testbestand eruit ziet. In uw eigen tests moet u veel uitingen hebben met de juiste intentie en door de machine geleerde entiteit die is gelabeld.

1. Maak `pizza-with-machine-learned-entity-test.json` in een teksteditor of [download](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) deze.

2. Voeg in het batchbestand met JSON-indeling een utterance toe met de **gewenste intentie** in de test.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>De batch uitvoeren

1. Selecteer **Testen** op de bovenste navigatiebalk.

2. Selecteer **Batch-testpaneel** in het deelvenster rechts.

3. Selecteer **Gegevensset Importeren**.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van luis-app met gegevensset Importeren gemarkeerd](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Kies de bestandslocatie `pizza-with-machine-learned-entity-test.json` van het bestand.

5. Geef de `pizza test` gegevensset een naam en selecteer **Gereed**.

    > [!div class="mx-imgBorder"]
    > ![Bestand selecteren](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Selecteer de knop **Run**.

7. Selecteer **Resultaten bekijken**.

8. Bekijk de resultaten in de grafiek en legenda.

## <a name="review-batch-results-for-intents"></a>Batchresultaten controleren op intents

De testresultaten laten grafisch zien hoe de testuitingen werden voorspeld tegen de actieve versie.

De batchgrafiek toont vier kwadranten met resultaten. Rechts van de grafiek staat een filter. Het filter bevat intenties en entiteiten. Wanneer u een [sectie van de grafiek](luis-concept-batch-test.md#batch-test-results) of een punt in de grafiek selecteert, wordt de bijbehorende utterance(s) onder de grafiek weergegeven.

Terwijl u over de grafiek zweeft, kan een muiswiel het scherm in de grafiek vergroten of verkleinen. Dit is handig wanneer er veel punten op de grafiek strak bij elkaar zijn geclusterd.

De grafiek is in vier kwadranten, met twee van de secties weergegeven in het rood.

1. Selecteer de intentie **Volgorde wijzigen** in de filterlijst.

    > [!div class="mx-imgBorder"]
    > ![Intentie wijzigen in filterlijst selecteren](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    De utterance wordt voorspeld als **een True Positive,** wat betekent dat de utterance met succes overeenkomt met de positieve voorspelling die in het batchbestand wordt vermeld.

    > [!div class="mx-imgBorder"]
    > ![Utterance heeft zijn positieve voorspelling met succes geëvenaard](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    De groene vinkjes in de lijst met filters geven ook het succes van de test voor elke intentie aan. Alle andere intenties worden vermeld met een 1/1 positieve score omdat de utterance is getest op elke intentie, als een negatieve test voor alle intenties die niet in de batchtest worden vermeld.

1. Selecteer de **intentie bevestiging.** Deze intentie wordt niet vermeld in de batchtest, dus dit is een negatieve test van de utterance die wordt vermeld in de batchtest.

    > [!div class="mx-imgBorder"]
    > ![Utterance met succes voorspeld negatief voor niet-vermelde intentie in batchbestand](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    De negatieve test was succesvol, zoals opgemerkt met de groene tekst in het filter, en het raster.

## <a name="review-batch-test-results-for-entities"></a>Batchtestresultaten voor entiteiten controleren

De entiteit ModifyOrder, als een machineentiteit met subentiteiten, geeft aan of de entiteit op het hoogste niveau overeenkomt en geeft weer hoe de subentiteiten worden voorspeld.

1. Selecteer de entiteit **Volgorde wijzigen** in de filterlijst en selecteer vervolgens de cirkel in het raster.

1. De entiteitsvoorspelling wordt onder de grafiek weergegeven. Het display bevat vaste regels voor voorspellingen die overeenkomen met de verwachting en stippellijnen voor voorspellingen die niet overeenkomen met de verwachting.

    > [!div class="mx-imgBorder"]
    > ![Bovenliggende entiteit die is voorspeld in batchbestand](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Fouten vinden met een batchtest

Deze zelfstudie liet je zien hoe je een test uitvoert en resultaten interpreteert. Het had geen betrekking op testfilosofie of hoe te reageren op falende tests.

* Zorg ervoor dat u zowel positieve als negatieve uitingen in uw test behandelt, inclusief uitingen die kunnen worden voorspeld voor een andere, maar verwante intentie.
* Voer de volgende taken uit voor het niet uitvoeren van de volgende taken en voer de tests opnieuw uit:
    * Controleer de huidige voorbeelden voor intents en entiteiten, valideer de voorbeelduitingen van de actieve versie zijn correct, zowel voor intent als entity labeling.
    * Functies toevoegen waarmee uw app intents en entiteiten voorspellen
    * Positievere voorbeelduitingen toevoegen
    * Saldo van voorbeelduitingen in intents controleren

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Volgende stap

De zelfstudie heeft een batchtest gebruikt om het huidige model te valideren.

> [!div class="nextstepaction"]
> [Meer informatie over patronen](luis-tutorial-pattern.md)

