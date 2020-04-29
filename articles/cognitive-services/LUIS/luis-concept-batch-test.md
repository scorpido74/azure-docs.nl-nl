---
title: Batch testen-LUIS
titleSuffix: Azure Cognitive Services
description: Gebruik batch tests om voortdurend aan uw toepassing te werken om deze te verfijnen en de taal overeenkomst te verbeteren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: e9ad7c52af20762633c710b39a64fbebf0cf6213
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220048"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Batch testen met 1000 uitingen in LUIS-Portal

Met batch tests wordt uw actieve getrainde versie gevalideerd om de nauw keurigheid van de voor spelling te meten. Met een batch test kunt u de nauw keurigheid van elke intentie en entiteit in uw actieve versie bekijken, waarbij de resultaten worden weer gegeven in een grafiek. Bekijk de resultaten van de batch test om passende maat regelen te nemen om de nauw keurigheid te verbeteren, zoals het toevoegen van meer voor beeld-uitingen aan een intentie als uw app regel matig de juiste intentie of label entiteiten in de utterance niet kan identificeren.

## <a name="group-data-for-batch-test"></a>Groeps gegevens voor batch test

Het is belang rijk dat uitingen dat wordt gebruikt voor het uitvoeren van batch tests, nieuw zijn in LUIS. Als u een gegevensset van uitingen hebt, moet u de uitingen in drie sets delen: voor beeld-uitingen die zijn toegevoegd aan een intentie, uitingen ontvangen van het gepubliceerde eind punt, en uitingen gebruikt voor batch test-LUIS nadat deze is getraind. 

## <a name="a-data-set-of-utterances"></a>Een gegevensset van uitingen

Verzend een batch-bestand van uitingen, ook wel een *gegevensset*genoemd, voor batch tests. De gegevensset is een bestand met JSON-indeling met een maximum van 1.000 gelabelde **niet-dubbele** uitingen. U kunt Maxi maal 10 gegevens sets testen in een app. Als u meer wilt testen, verwijdert u een gegevensset en voegt u een nieuwe toe.

|**Wetgeving**|
|--|
|* Geen dubbele uitingen|
|1000 uitingen of minder|

* Dubbele waarden worden beschouwd als exacte teken reeks overeenkomsten, niet overeenkomt met het eerste token. 

## <a name="entities-allowed-in-batch-tests"></a>Entiteiten die zijn toegestaan in batch tests

Alle aangepaste entiteiten in het model worden weer gegeven in het filter voor batch test entiteiten, zelfs als er geen overeenkomende entiteiten in de batch bestands gegevens zijn.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Batch-bestands indeling

Het batch bestand bestaat uit uitingen. Elke utterance moet een verwachte intentie voorspelling hebben, samen met alle entiteiten die door [machines zijn geleerd](luis-concept-entity-types.md#types-of-entities) die u verwacht te detecteren. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Sjabloon voor batch-syntaxis voor intentiesen met entiteiten

Gebruik de volgende sjabloon om uw batch-bestand te starten:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Het batch-bestand maakt gebruik van de eigenschappen **startPos** en **endPos** om het begin en het einde van een entiteit te noteren. De waarden zijn op nul gebaseerd en mogen niet beginnen of eindigen op een spatie. Dit wijkt af van de query logboeken die eigenschappen start index en endIndex gebruiken. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Sjabloon voor batch-syntaxis voor intentiesen zonder entiteiten

Gebruik de volgende sjabloon om uw batch-bestand te starten zonder entiteiten:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Als u geen entiteiten wilt testen, neemt u de `entities` eigenschap op en stelt u de waarde in als een lege `[]`matrix.


## <a name="common-errors-importing-a-batch"></a>Veelvoorkomende fouten bij het importeren van een batch

Veelvoorkomende fouten zijn onder andere: 

> * Meer dan 1.000 uitingen
> * Een utterance JSON-object dat geen eigenschap entities heeft. De eigenschap kan een lege matrix zijn.
> * Woord (en) gemarkeerd in meerdere entiteiten
> * Het entiteit label begint of eindigt op een spatie.

## <a name="batch-test-state"></a>Batch-test status

LUIS houdt de status van de laatste test van elke gegevensset bij. Dit omvat de grootte (aantal uitingen in de batch), de laatste uitvoerings datum en het laatste resultaat (aantal voorspelde uitingen).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Resultaten batch-test

Het resultaat van de batch test is een spreidings grafiek, ook wel een fout matrix genoemd. Deze grafiek is een vier richtings vergelijking van de uitingen in het batch bestand en het voor speld doel van het huidige model en de entiteiten. 

Gegevens punten in de **onjuiste, positieve** en **onjuiste negatieve** secties geven fouten aan, die moeten worden onderzocht. Als alle gegevens punten zich op de **echte positieve** en **echte negatieve** secties bevinden, is de nauw keurigheid van uw app perfect voor deze gegevens verzameling.

![Vier secties van de grafiek](./media/luis-concept-batch-test/chart-sections.png)

Deze grafiek helpt u uitingen te vinden die LUIS op onjuiste wijze voorspeld op basis van de huidige training. De resultaten worden weer gegeven per regio van de grafiek. Selecteer afzonderlijke punten in de grafiek om de utterance-informatie te bekijken of Selecteer regio naam om utterance resultaten in die regio te bekijken.

![Batchgewijs testen](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Fouten in de resultaten

Fouten in de batch-test geven aan dat intenties niet worden voor speld zoals vermeld in het batch-bestand. Fouten worden aangegeven in de twee rode secties van de grafiek. 

De sectie ' fout positief ' geeft aan dat een utterance overeenkomt met een intentie of entiteit wanneer deze niet had moeten beschikken. De onjuiste waarde geeft aan dat een utterance niet overeenkomt met een intentie of entiteit wanneer deze zou moeten hebben. 

## <a name="fixing-batch-errors"></a>Batch fouten corrigeren

Als er fouten zijn opgetreden tijdens het testen van de batch, kunt u meer uitingen toevoegen aan een intentie en/of een label meer uitingen met de entiteit om LUIS de discriminatie tussen de intenties te maken. Als u uitingen hebt toegevoegd en deze een label krijgt en nog steeds Voorspellings fouten in batch tests ontvangt, kunt u een [woordgroepen lijst](luis-concept-feature.md) functie toevoegen met een Documentspecifieke vocabulaire om Luis sneller te leren. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [testen van een batch](luis-how-to-batch-test.md)
