---
title: Batch testen - LUIS
titleSuffix: Azure Cognitive Services
description: Gebruik batchtesten om continu aan uw toepassing te werken om deze te verfijnen en het taalbegrip te verbeteren.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220048"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Batchtesten met 1000 uitingen in LUIS-portal

Batchtesten valideert uw actieve getrainde versie om de nauwkeurigheid van de voorspelling te meten. Met een batchtest u de nauwkeurigheid van elke intentie en entiteit in uw actieve versie weergeven en resultaten weergeven met een grafiek. Controleer de batchtestresultaten om de juiste actie te ondernemen om de nauwkeurigheid te verbeteren, zoals het toevoegen van meer voorbeelduitingen aan een intentie als uw app vaak niet de juiste intentie of etiketteringsentiteiten in de utterance identificeert.

## <a name="group-data-for-batch-test"></a>Groepsgegevens voor batchtest

Het is belangrijk dat uitingen die worden gebruikt voor batchtesten nieuw zijn voor LUIS. Als u een gegevensset met uitingen hebt, verdeelt u de uitingen in drie sets: voorbeelduitingen die zijn toegevoegd aan een intentie, uitingen die zijn ontvangen van het gepubliceerde eindpunt en uitingen die worden gebruikt om LUIS te batchtesten nadat deze is getraind. 

## <a name="a-data-set-of-utterances"></a>Een gegevensset met uitingen

Dien een batchbestand met uitingen in, ook wel *een gegevensset genoemd,* voor batchtests. De gegevensset is een JSON-geformatteerd bestand met maximaal 1.000 gelabelde **niet-dubbele** uitingen. U maximaal 10 gegevenssets testen in een app. Als u meer wilt testen, verwijdert u een gegevensset en voegt u een nieuwe toe.

|**Regels**|
|--|
|*Geen dubbele uitingen|
|1000 uitingen of minder|

*Duplicaten worden beschouwd als exacte tekenreeksovereenkomsten, niet als overeenkomsten die eerst worden getokeniseerd. 

## <a name="entities-allowed-in-batch-tests"></a>Entiteiten die zijn toegestaan in batchtests

Alle aangepaste entiteiten in het model worden weergegeven in het filter batchtestentiteiten, zelfs als er geen overeenkomstige entiteiten in de batchbestandsgegevens zijn.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Batchbestandsindeling

Het batchbestand bestaat uit uitingen. Elke utterance moet een verwachte intentievoorspelling hebben, samen met alle [door de machine aangeleerde entiteiten die](luis-concept-entity-types.md#types-of-entities) u verwacht te detecteren. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Syntaxissjabloon batch voor intents with entiteiten

Gebruik de volgende sjabloon om uw batchbestand te starten:

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

Het batchbestand gebruikt de eigenschappen **startPos** en **endPos** om het begin en einde van een entiteit te noteren. De waarden zijn op nul gebaseerd en mogen niet beginnen of eindigen op een spatie. Dit is anders dan de querylogboeken, die startIndex- en endIndex-eigenschappen gebruiken. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Syntaxissjabloon batch voor intents zonder entiteiten

Gebruik de volgende sjabloon om uw batchbestand zonder entiteiten te starten:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Als u geen entiteiten wilt testen, neemt u de `entities` eigenschap `[]`op en stelt u de waarde in als een lege array.


## <a name="common-errors-importing-a-batch"></a>Veelvoorkomende fouten bij het importeren van een batch

Veelvoorkomende fouten zijn: 

> * Meer dan 1.000 uitingen
> * Een UTTERANCE JSON-object dat geen eigenschap van entiteiten heeft. De eigenschap kan een lege array zijn.
> * Word(s) gelabeld in meerdere entiteiten
> * Entiteitslabel dat begint of eindigt op een spatie.

## <a name="batch-test-state"></a>Batchteststatus

LUIS volgt de status van de laatste test van elke gegevensset. Dit omvat de grootte (aantal uitingen in de batch), laatste rundatum en laatste resultaat (aantal met succes voorspelde uitingen).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Batchtestresultaten

Het resultaat van de batchtest is een spreidingsgrafiek, bekend als een foutmatrix. Deze grafiek is een 4-weg vergelijking van de uitingen in het batchbestand en de voorspelde intentie en entiteiten van het huidige model. 

Gegevenspunten op de secties **False Positive** and **False Negative** geven fouten aan, die moeten worden onderzocht. Als alle gegevenspunten zich op de secties **True Positive** en **True Negative** bevinden, is de nauwkeurigheid van uw app perfect op deze gegevensset.

![Vier secties van grafiek](./media/luis-concept-batch-test/chart-sections.png)

Met deze grafiek u uitingen vinden die LUIS onjuist voorspelt op basis van de huidige training. De resultaten worden weergegeven per regio van de grafiek. Selecteer afzonderlijke punten in de grafiek om de utterance-informatie te controleren of selecteer de regionaam om de utteranceresultaten in dat gebied te bekijken.

![Batchgewijs testen](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Fouten in de resultaten

Fouten in de batchtest geven intenties aan die niet worden voorspeld zoals vermeld in het batchbestand. Fouten worden aangegeven in de twee rode secties van de grafiek. 

De fout-positieve sectie geeft aan dat een utterance overeenkomt met een intentie of entiteit wanneer deze niet had moeten worden uitgevoerd. Het false-negatief geeft aan dat een utterance niet overeenkomt met een intentie of entiteit wanneer deze zou moeten hebben. 

## <a name="fixing-batch-errors"></a>Batchfouten oplossen

Als er fouten zijn in het testen van de batch, u meer uitingen toevoegen aan een intentie en/of meer uitingen met de entiteit labelen om LUIS te helpen de onderscheid tussen intenties te maken. Als u uitingen hebt toegevoegd en deze hebt gelabeld en nog steeds voorspellingsfouten in batchtests krijgt, u overwegen een [functie met woordgroeplijst](luis-concept-feature.md) toe te voegen met domeinspecifieke woordenschat om LUIS te helpen sneller te leren. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [testen van een batch](luis-how-to-batch-test.md)
