---
title: Functies-LUIS
titleSuffix: Azure Cognitive Services
description: Voeg functies toe aan een taal model om tips te bieden over het herkennen van de invoer die u wilt labelen of classificeren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 0cab6eb38459a632f1e7bd1a21e6a7251d33f683
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647423"
---
# <a name="machine-learned-features"></a>Door de machine geleerde functies 

In machine learning is een *functie* een onderscheiding van eigenschappen of kenmerken van gegevens die uw systeem observeert & meer informatie. In Language Understanding (LUIS) wordt een functie beschreven en uitgelegd wat belang rijk is voor uw intentie en entiteiten.

In de [Preview-Luis-Portal](https://preview.luis.ai)zijn functies _descriptors_ , omdat ze worden gebruikt om de intentie of entiteit te _beschrijven_ .  

## <a name="features-_descriptors_-in-language-understanding"></a>Functies (_descriptors_) in language Understanding

Functies, ook wel descriptors genoemd, beschrijven aanwijzingen om Language Understanding te helpen bij het identificeren van het voor beeld uitingen. Functies zijn onder andere: 

* Woordgroepen lijst als een functie voor intenties of entiteiten
* Entiteiten als functies voor intenties of entiteiten

Functies moeten worden beschouwd als een nood zakelijk deel van het schema voor het desamen stellen van modellen. 

## <a name="what-is-a-phrase-list"></a>Wat is een woordgroepen lijst

Een woordgroepen lijst is een lijst met woorden, zinsdelen, cijfers of andere tekens die u helpen bij het identificeren van het concept dat u probeert te identificeren. De lijst is hoofdletter gevoelig. 

## <a name="when-to-use-a-phrase-list"></a>Wanneer u een woordgroepen lijst gebruikt

Met een woordgroepen lijst, beschouwt LUIS context en generaliseert om items te identificeren die vergelijkbaar zijn met, maar niet exact overeenkomen met de tekst. Als u wilt dat uw LUIS-app nieuwe items kan generaliseren en identificeren, gebruikt u een woordgroepen lijst. 

Als u nieuwe instanties wilt kunnen herkennen, zoals een Meeting scheduler die de namen van nieuwe contact personen moet herkennen of een inventarisatie-app die nieuwe producten moet herkennen, start u met een door de machine geleerde entiteit. Maak vervolgens een woordgroepen lijst waarmee LUIS woorden met een vergelijk bare betekenis kunt vinden. In deze woordgroepen lijst staan hand leidingen LUIS om voor beelden te herkennen door extra significantie toe te voegen aan de waarde van deze woorden. 

Woordgroepen lijsten zijn net als een Documentspecifieke woorden lijst waarmee u de kwaliteit van het memorandum van intenties en entiteiten kunt verbeteren. 

## <a name="considerations-when-using-a-phrase-list"></a>Overwegingen bij het gebruik van een woordgroepen lijst

Standaard wordt een woordgroepen lijst toegepast op alle modellen in de app. Dit werkt voor woordgroepen lijsten die alle intents en entiteiten kunnen kruisen. Voor de ontsamen stel moet u een woordgroepen lijst Toep assen op alleen de modellen die relevant zijn voor. 

Als u een woordgroepen lijst maakt (deze is standaard globaal gemaakt) en deze vervolgens later als descriptor (functie) Toep assen op een specifiek model, wordt deze verwijderd uit de andere modellen. Deze verwijdering voegt relevantie toe aan de lijst met zinsdelen voor het model waarop deze wordt toegepast, waardoor de nauw keurigheid van het model kan worden verbeterd. 

De vlag `enabledForAllModels` bepaalt dit model bereik in de API. 

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Een woordgroepen lijst gebruiken

[Maak een lijst met woordgroepen lijsten](luis-how-to-add-features.md) als uw intentie of entiteit woorden of zinsdelen bevat die belang rijk zijn zoals:

* branche voorwaarden
* slang
* afkortingen
* bedrijfsspecifieke taal
* taal die afkomstig is uit een andere taal, maar vaak wordt gebruikt in uw app
* tref woorden en zinsdelen in uw voor beeld uitingen

Voeg **niet** alle mogelijke woorden of woord groepen toe. Voeg in plaats daarvan enkele woorden of woord groepen tegelijk toe, vervolgens opnieuw te trainen en te publiceren. Wanneer de lijst over een periode groeit, zijn er mogelijk veel formulieren (synoniemen). Deze in een andere lijst opsplitsen. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Wanneer een entiteit moet worden gebruikt als een functie 

Een entiteit kan worden toegevoegd als een functie op het niveau van intentie of entiteit. 

### <a name="entity-as-a-feature-to-an-intent"></a>Entiteit als een functie voor een intentie

Voeg een entiteit als een descriptor (functie) toe aan een intentie wanneer de detectie van die entiteit significant is voor het doel.

Als de bedoeling bijvoorbeeld is voor het reserveren van een vlucht en de entiteit ticket gegevens bevat (zoals het aantal seats, de oorsprong en de bestemming), moet de entiteit ticket gegevens een weging toevoegen aan de voor spelling van de bedoeling van de boek vlucht. 

### <a name="entity-as-a-feature-to-another-entity"></a>Entiteit als een functie van een andere entiteit

Een entiteit (A) moet worden toegevoegd als een functie aan een andere entiteit (B) wanneer de detectie van die entiteit (A) belang rijk is voor de (B).

Als bijvoorbeeld de adres entiteit (A) wordt gedetecteerd, wordt door het adres (A) het gewicht (A) opgeteld bij de voor spelling voor de entiteit van het verzend adres (B). 

## <a name="best-practices"></a>Aanbevolen procedures
Lees de [Aanbevolen procedures](luis-concept-best-practices.md).

## <a name="next-steps"></a>Volgende stappen

Zie [onderdelen toevoegen](luis-how-to-add-features.md) voor meer informatie over het toevoegen van functies aan uw Luis-app.