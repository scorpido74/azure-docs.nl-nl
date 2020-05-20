---
title: Een batch test uitvoeren-LUIS
titleSuffix: Azure Cognitive Services
description: Gebruik Language Understanding (LUIS) batch test sets om uitingen te vinden met onjuiste doel stellingen en entiteiten.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 4d3535d419b0f99491795b3920fdd295e3128299
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654014"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Batch tests met een set voor beeld-uitingen

 Batch tests is een uitgebreide test op uw huidige getrainde model om de prestaties in LUIS te meten. De gegevens sets die worden gebruikt voor batch tests mogen geen voor beeld-uitingen bevatten in de intenties of uitingen die worden ontvangen van het voor spelling runtime-eind punt.

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Een gegevenssetbestand importeren voor batch tests

1. Selecteer **test** in de bovenste balk en selecteer vervolgens **het deel venster voor batch testen**.

    ![Koppeling voor batch testen](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Selecteer **gegevensset importeren**. Het dialoog venster **nieuwe gegevensset importeren** wordt weer gegeven. Selecteer **bestand kiezen** en zoek naar een JSON-bestand met de juiste [JSON-indeling](luis-concept-batch-test.md#batch-file-format) die *niet meer dan 1.000* uitingen bevat om te testen.

    Import fouten worden gerapporteerd in een rode meldingen balk boven aan de browser. Wanneer een import fouten bevat, wordt er geen gegevensset gemaakt. Zie [common Errors](luis-concept-batch-test.md#common-errors-importing-a-batch)(Engelstalig) voor meer informatie.

3. Voer in het veld **naam van gegevensset** een naam in voor het gegevenssetbestand. Het gegevenssetbestand bevat een **matrix met uitingen** , met inbegrip van het *Label intentie* en *entiteiten*. Bekijk het [voor beeld van een batch bestand](luis-concept-batch-test.md#batch-file-format) op syntaxis.

4. Selecteer **Done**. Het gegevenssetbestand wordt toegevoegd.

## <a name="run-rename-export-or-delete-dataset"></a>Gegevensset uitvoeren, naam wijzigen, exporteren of verwijderen

Als u de gegevensset wilt uitvoeren, verwijderen of de naam ervan wilt wijzigen, gebruikt u de knop met het weglatings teken (***...***) aan het einde van de rij van de gegevensset.

> [!div class="mx-imgBorder"]
> ![Scherm opname van batch tests lijst met opties](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Een batch test uitvoeren op uw getrainde app

Als u de test wilt uitvoeren, selecteert u de naam van de gegevensset en selecteert u **uitvoeren** op de contextuele werk balk. Wanneer de test is voltooid, wordt in deze rij het test resultaat van de gegevensset weer gegeven.

De gegevensset die u wilt downloaden is hetzelfde als het bestand dat is geüpload voor batch tests.

|Staat|Betekenis|
|--|--|
|![Groene cirkel pictogram met geslaagde test](./media/luis-how-to-batch-test/batch-test-result-green.png)|Alle uitingen zijn geslaagd.|
|![Rode x-pictogram testen mislukt](./media/luis-how-to-batch-test/batch-test-result-red.png)|Ten minste één utterance-intentie komt niet overeen met de voor spelling.|
|![Pictogram gereed voor test](./media/luis-how-to-batch-test/batch-test-result-blue.png)|De test is gereed om te worden uitgevoerd.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Test resultaten van batch weer geven

Selecteer **resultaten weer geven**om de resultaten van de batch test te controleren.

<a name="filter-chart-results-by-intent-or-entity"></a>

## <a name="filter-chart-results"></a>Grafiek resultaten filteren

Als u de grafiek wilt filteren op een specifieke intentie of entiteit, selecteert u de intentie of entiteit in het deel venster filter aan de rechter kant. De gegevens punten en hun distributie-update in de grafiek volgens uw selectie.

![Gevisualiseerd batch test resultaat](./media/luis-how-to-batch-test/filter-by-entity.png)

## <a name="view-single-point-utterance-data"></a>Utterance gegevens met één punt weer geven

Beweeg de muis aanwijzer over een gegevens punt in de grafiek om de zekerheids Score van de voor spelling te zien. Selecteer een gegevens punt om de bijbehorende utterance op te halen in de lijst met uitingen aan de onderkant van de pagina.

![Geselecteerde utterance](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Sectie gegevens weer geven

Selecteer in het diagram met vier secties de naam van de sectie, zoals **Onwaar positief** in de rechter bovenhoek van de grafiek. Onder de grafiek worden alle uitingen in dat gedeelte onder de grafiek in een lijst weer gegeven.

![Geselecteerde uitingen per sectie](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

In deze voor gaande afbeelding wordt de utterance `switch on` aangeduid met de TurnAllOn intentie, maar is de voor spelling van geen intentie ontvangen. Dit is een indicatie dat de TurnAllOn-intentie meer voorbeeld uitingen nodig heeft om de verwachte voor spelling te maken.

De twee secties van de grafiek worden rood aangeduid met uitingen die niet overeenkomen met de verwachte voor spelling. Deze geven aan uitingen welke LUIS meer training nodig heeft.

De twee secties van de grafiek in het groen komen overeen met de verwachte voor spelling.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Volgende stappen

Als met testen wordt aangegeven dat uw LUIS-app de juiste intenties en entiteiten niet herkent, kunt u werken om de prestaties van uw LUIS-app te verbeteren door meer uitingen te labelen of functies toe te voegen.

* [Aanbevolen uitingen label met LUIS](luis-how-to-review-endpoint-utterances.md)
* [Functies gebruiken om de prestaties van uw LUIS-app te verbeteren](luis-how-to-add-features.md)
* [Meer informatie over batch tests met deze zelf studie](luis-tutorial-batch-testing.md)
* [Meer informatie over het testen van batches](luis-concept-batch-test.md).
