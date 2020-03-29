---
title: Een batchtest uitvoeren - LUIS
titleSuffix: Azure Cognitive Services
description: Luis-batchtestsets (Language Understanding) gebruiken om uitingen met onjuiste intenties en entiteiten te vinden.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: bfef7eae7158a05b09a3534e8fb44335333d8cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904356"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Batchtesten met een reeks voorbeelduitingen

 Batchtesten is een uitgebreide test op uw huidige getrainde model om de prestaties ervan in LUIS te meten. De gegevenssets die worden gebruikt voor batchtests mogen geen voorbeelduitingen bevatten in de intents of uitingen die zijn ontvangen van het eindpunt voor de prognoseruntime. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Een gegevenssetbestand importeren voor batchtests

1. Selecteer **Testen** op de bovenste balk en selecteer **vervolgens het testpaneel Batch**.

    ![Koppeling batchtesten](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Selecteer **Gegevensset Importeren**. Het dialoogvenster **Nieuwe gegevensset importeren** wordt weergegeven. Selecteer **Bestand kiezen** en zoek een JSON-bestand met de juiste [JSON-indeling](luis-concept-batch-test.md#batch-file-format) die *niet meer dan 1.000* uitingen bevat om te testen.

    Importfouten worden gemeld in een rode meldingsbalk boven aan de browser. Wanneer een import fouten heeft, wordt er geen gegevensset gemaakt. Zie [Veelvoorkomende fouten voor](luis-concept-batch-test.md#common-errors-importing-a-batch)meer informatie .

3. Voer in het veld **Gegevenssetnaam** een naam in voor uw gegevenssetbestand. Het gegevenssetbestand bevat een **array met uitingen,** waaronder de *gelabelde intentie* en *entiteiten.* Controleer het [voorbeeldbatchbestand](luis-concept-batch-test.md#batch-file-format) op syntaxis. 

4. Selecteer **Done**. Het gegevenssetbestand wordt toegevoegd.

## <a name="run-rename-export-or-delete-dataset"></a>Gegevensset uitvoeren, hernoemen, exporteren of verwijderen

Als u de gegevensset wilt uitvoeren, hernoemen, exporteren of verwijderen, gebruikt u de knop alipse (***...***) aan het einde van de gegevenssetrij.

![Gegevenssetacties](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Een batchtest uitvoeren op uw getrainde app

Als u de test wilt uitvoeren, selecteert u de naam van de gegevensset. Wanneer de test is voltooid, wordt in deze rij het testresultaat van de gegevensset weergegeven.

![Batchtestresultaat](./media/luis-how-to-batch-test/run-test.png)

De downloadbare gegevensset is hetzelfde bestand dat is geüpload voor batchtests.

|Status|Betekenis|
|--|--|
|![Pictogram Succesvolle testgroene cirkel](./media/luis-how-to-batch-test/batch-test-result-green.png)|Alle uitingen zijn succesvol.|
|![Pictogram Niet-testrood x](./media/luis-how-to-batch-test/batch-test-result-red.png)|Ten minste één utterance intent kwam niet overeen met de voorspelling.|
|![Pictogram Klaar om te testen](./media/luis-how-to-batch-test/batch-test-result-blue.png)|De test is klaar om te worden uitgevoerd.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Resultaten van batchtests weergeven 

Als u de resultaten van de batchtest wilt bekijken, selecteert u **Resultaten weergeven**.

![Batchtestresultaten](./media/luis-how-to-batch-test/run-test-results.png)

<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>Grafiekresultaten filteren

Als u de grafiek wilt filteren op een specifieke intentie of entiteit, selecteert u de intentie of entiteit in het filterpaneel aan de rechterkant. De gegevenspunten en hun distributie-update in de grafiek volgens uw selectie. 
 
![Gevisualiseerd batchtestresultaat](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Utterancegegevens met één punt weergeven

In de grafiek, zweven over een gegevenspunt om de zekerheid score van de voorspelling te zien. Selecteer een gegevenspunt om de bijbehorende utterance op te halen in de lijst met uitingen onder aan de pagina. 

![Geselecteerde utterance](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Sectiegegevens weergeven

Selecteer in het viersectiediagram de sectienaam, zoals **False Positive** rechtsboven in de grafiek. Onder de grafiek worden alle uitingen in die sectie weergegeven onder de grafiek in een lijst. 

![Geselecteerde uitingen per sectie](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

In deze voorgaande afbeelding `switch on` wordt de utterance gelabeld met de Intentie TurnAllOn, maar de voorspelling van Geen intentie ontvangen. Dit is een indicatie dat de TurnAllOn-intentie meer voorbeelduitingen nodig heeft om de verwachte voorspelling te kunnen doen. 

De twee secties van de grafiek in het rood geven uitingen aan die niet overeenkomen met de verwachte voorspelling. Deze geven uitingen aan die LUIS meer training nodig heeft. 

De twee secties van de grafiek in het groen kwam overeen met de verwachte voorspelling.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Volgende stappen

Als uit testen blijkt dat uw LUIS-app de juiste intenties en entiteiten niet herkent, u de prestaties van uw LUIS-app verbeteren door meer uitingen te labelen of functies toe te voegen. 

* [Voorgestelde uitingen labelen met LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Functies gebruiken om de prestaties van uw LUIS-app te verbeteren](luis-how-to-add-features.md) 
* [Inzicht in batchtesten met deze zelfstudie](luis-tutorial-batch-testing.md)
* [Meer informatie over batchtestconcepten](luis-concept-batch-test.md).
