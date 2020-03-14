---
title: Batch testen-LUIS
titleSuffix: Azure Cognitive Services
description: Gebruik batch testen om te werken continu op uw toepassing het verfijnen en verbeteren van de taal begrijpen.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220048"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Batch testen met 1000 uitingen in LUIS-Portal

Met batch tests wordt uw actieve getrainde versie gevalideerd om de nauw keurigheid van de voor spelling te meten. Met een batch test kunt u de nauw keurigheid van elke intentie en entiteit in uw actieve versie bekijken, waarbij de resultaten worden weer gegeven in een grafiek. Bekijk de resultaten van de batch test om passende maat regelen te nemen om de nauw keurigheid te verbeteren, zoals het toevoegen van meer voor beeld-uitingen aan een intentie als uw app regel matig de juiste intentie of label entiteiten in de utterance niet kan identificeren.

## <a name="group-data-for-batch-test"></a>Groepsgegevens voor batch-test

Het is belangrijk dat uitingen die wordt gebruikt voor het testen van de batch niet bekend bent met LUIS. Als u een gegevensset van uitingen hebt, moet u de uitingen in drie sets delen: voor beeld-uitingen die zijn toegevoegd aan een intentie, uitingen ontvangen van het gepubliceerde eind punt, en uitingen gebruikt voor batch test-LUIS nadat deze is getraind. 

## <a name="a-data-set-of-utterances"></a>Een gegevensset van uitingen

Verzend een batch-bestand van uitingen, ook wel een *gegevensset*genoemd, voor batch tests. De gegevensset is een bestand met JSON-indeling met een maximum van 1.000 gelabelde **niet-dubbele** uitingen. U kunt Maxi maal 10 gegevens sets testen in een app. Als u meer wilt testen, verwijdert u een gegevensset en voegt u een nieuwe toe.

|**Wetgeving**|
|--|
|\* Er zijn geen dubbele uitingen|
|uitingen van 1000 of minder|

\* Dubbele waarden worden beschouwd als de exacte tekenreeks komt overeen met, niet overeenkomt met die eerst worden getokeniseerd. 

## <a name="entities-allowed-in-batch-tests"></a>Entiteiten die zijn toegestaan in de batchtests

Alle aangepaste entiteiten in het model worden weergegeven in het filter entiteiten van batch test, zelfs als er geen bijbehorende entiteiten in de gegevens van de batch-bestand zijn.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Batch-bestandsindeling

De batch-bestand bestaat uit uitingen. Elke utterance moet een verwachte intentie voorspelling hebben, samen met alle entiteiten die door [machines zijn geleerd](luis-concept-entity-types.md#types-of-entities) die u verwacht te detecteren. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Sjabloon voor batch-syntaxis voor intentiesen met entiteiten

Gebruik de volgende sjabloon om te starten van uw batch-bestand:

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

Het batch-bestand maakt gebruik van de eigenschappen **startPos** en **endPos** om het begin en het einde van een entiteit te noteren. De waarden mag op nul gebaseerde zijn en niet beginnen of eindigen op een spatie. Dit wijkt af van de logboeken voor query's, die startIndex en endIndex eigenschappen gebruiken. 

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

Als u geen entiteiten wilt testen, neemt u de eigenschap `entities` op en stelt u de waarde in als een lege matrix, `[]`.


## <a name="common-errors-importing-a-batch"></a>Veelvoorkomende fouten in een batch importeren

Veelvoorkomende fouten zijn onder andere: 

> * Uitingen van meer dan 1000
> * Een utterance JSON-object dat niet een eigenschap entiteiten hebben. De eigenschap mag geen lege matrix zijn.
> * Woorden die met het label in meerdere entiteiten
> * Label van de entiteit beginnen of eindigen op een spatie.

## <a name="batch-test-state"></a>Status van de batch-test

LUIS houdt de status van de laatste test van elke gegevensset bij. Dit omvat de grootte (aantal uitingen in de batch), voor het laatst uitgevoerd datum en de laatste resultaat (aantal is voorspelde uitingen).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>De resultaten van batch

Het resultaat van de test batch is een spreidingsgrafiek bekend als een matrix van de fout. Deze grafiek bevat een vergelijking 4-weg van de uitingen in de batch-bestand en het huidige model voorspelde intentie en entiteiten. 

Gegevens punten in de **onjuiste, positieve** en **onjuiste negatieve** secties geven fouten aan, die moeten worden onderzocht. Als alle gegevens punten zich op de **echte positieve** en **echte negatieve** secties bevinden, is de nauw keurigheid van uw app perfect voor deze gegevens verzameling.

![Vier secties van grafiek](./media/luis-concept-batch-test/chart-sections.png)

In deze grafiek helpt u uitingen die LUIS voorspelt ten onrechte op basis van de huidige training te vinden. De resultaten worden weergegeven per regio van de grafiek. Selecteer afzonderlijke punten in de grafiek Lees de informatie utterance of selecteer regionaam utterance resultaten in die regio bekijken.

![Batchgewijs testen](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Fouten in de resultaten

Fouten in de batch-test duiden intents die niet worden voorspeld, zoals vermeld in de batch-bestand. Fouten worden aangegeven in de twee rode secties van de grafiek. 

De waarde false positieve sectie geeft aan dat een utterance overeenkomen met een doel of de entiteit als het al dan niet mogen hebben. De negatieve waarde false geeft aan dat een utterance komt niet overeen met een doel of de entiteit wanneer deze moet hebben. 

## <a name="fixing-batch-errors"></a>Batchfouten oplossen

Als er fouten in de batch-tests, u kunt een meer utterances toevoegen aan een doel en/of meer uitingen aan de entiteit te maken van het onderscheid tussen intents LUIS label. Als u uitingen hebt toegevoegd en deze een label krijgt en nog steeds Voorspellings fouten in batch tests ontvangt, kunt u een [woordgroepen lijst](luis-concept-feature.md) functie toevoegen met een Documentspecifieke vocabulaire om Luis sneller te leren. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [testen van een batch](luis-how-to-batch-test.md)
