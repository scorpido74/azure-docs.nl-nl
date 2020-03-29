---
title: Kenmerken - LUIS
titleSuffix: Azure Cognitive Services
description: Voeg functies toe aan een taalmodel om hints te geven over het herkennen van invoer die u wilt labelen of classificeren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: b151043babd6c67a17f704bb671a415fcc3ba7b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220994"
---
# <a name="machine-learned-features"></a>Door machines geleerde functies 

In machine learning is een _functie_ een onderscheidende eigenschap of kenmerk van gegevens die uw systeem waarneemt & leert door. In Language Understanding (LUIS) beschrijft en legt een functie uit wat belangrijk is aan uw intenties en entiteiten.

In de [preview LUIS-portal](https://preview.luis.ai)worden functies _beschrijvingen_ gegeven omdat ze worden gebruikt om de intentie of entiteit te _beschrijven._  

## <a name="features-_descriptors_-in-language-understanding"></a>Functies _(beschrijvingen)_ in taalbegrip

Functies, ook wel beschrijvingsbepalingen genoemd, beschrijven aanwijzingen om taalinzicht te helpen bij het identificeren van de voorbeelduitingen. Functies zijn onder andere: 

* Lijst met woordgroepen als functie voor intents of entiteiten
* Entiteiten als functies voor intents of entiteiten

Functies moeten worden beschouwd als een noodzakelijk onderdeel van uw schema voor modelontbinding. 

## <a name="what-is-a-phrase-list"></a>Wat is een woordenlijst

Een woordenlijst is een lijst met woorden, zinnen, getallen of andere tekens die helpen bij het identificeren van het concept dat u probeert te identificeren. De lijst is case-ongevoelig. 

## <a name="when-to-use-a-phrase-list"></a>Wanneer een woordenlijst gebruiken

Met een woordenlijst houdt LUIS rekening met context en generaliseert om items te identificeren die vergelijkbaar zijn met, maar niet een exacte tekstovereenkomst. Als u uw LUIS-app nodig hebt om nieuwe items te kunnen generaliseren en te identificeren, gebruikt u een woordenlijst. 

Als u nieuwe exemplaren wilt kunnen herkennen, zoals een vergaderingsplanner die de namen van nieuwe contactpersonen moet herkennen of een voorraad-app die nieuwe producten moet herkennen, begint u met een door machines geleerde entiteit. Maak vervolgens een woordenlijst waarmee LUIS woorden met dezelfde betekenis kan vinden. Met deze woordenlijst wordt LUIS handleidingom voorbeelden te herkennen door extra betekenis toe te voegen aan de waarde van die woorden. 

Woordlijsten zijn als domeinspecifieke woordenschat die helpen bij het verbeteren van de kwaliteit van het begrip van zowel intents als entiteiten. 

## <a name="considerations-when-using-a-phrase-list"></a>Overwegingen bij het gebruik van een woordenlijst

Een woordenlijst wordt standaard toegepast op alle modellen in de app. Dit werkt voor woordgroeplijsten die alle intenties en entiteiten kunnen kruisen. Voor ontbouwbaarheid moet u een woordgroeplijst toepassen op alleen de modellen waarvoor deze relevant is. 

Als u een woordgroepmaakt (standaard wereldwijd gemaakt) en deze later als beschrijving (functie) toepast op een specifiek model, wordt deze verwijderd uit de andere modellen. Deze verwijdering voegt relevantie toe aan de woordgroeplijst voor het model dat wordt toegepast, waardoor de nauwkeurigheid die het in het model biedt, wordt verbeterd. 

De `enabledForAllModels` vlag bepaalt dit modelbereik in de API. 

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Een woordenlijst gebruiken

[Maak een woordenlijst](luis-how-to-add-features.md) wanneer uw intentie of entiteit woorden of zinnen heeft die belangrijk zijn, zoals:

* industrievoorwaarden
* Slang
* Afkortingen
* bedrijfsspecifieke taal
* taal die uit een andere taal komt, maar vaak wordt gebruikt in uw app
* trefwoorden en zinnen in uw voorbeelduitingen

Voeg **niet** elk mogelijk woord of zin. Voeg in plaats daarvan een paar woorden of zinnen tegelijk toe en train en publiceer. Naarmate de lijst in de loop van de tijd groeit, u merken dat sommige termen veel vormen hebben (synoniemen). Breek deze uit in een andere lijst. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Wanneer een entiteit als functie gebruiken 

Een entiteit kan worden toegevoegd als een functie op de intentie of het entiteitsniveau. 

### <a name="entity-as-a-feature-to-an-intent"></a>Entiteit als functie voor een intentie

Voeg een entiteit als beschrijving (functie) toe aan een intentie wanneer de detectie van die entiteit belangrijk is voor de intentie.

Als het bijvoorbeeld de bedoeling is om een vlucht te boeken en de entiteit ticketinformatie is (zoals het aantal stoelen, de oorsprong en de bestemming), moet het vinden van de entiteit van de ticketinformatie gewicht toevoegen aan de voorspelling van de intentie van de boekvlucht. 

### <a name="entity-as-a-feature-to-another-entity"></a>Entiteit als functie voor een andere entiteit

Een entiteit (A) moet als functie aan een andere entiteit (B) worden toegevoegd wanneer de detectie van die entiteit (A) belangrijk is voor de voorspelling van entiteit (B).

Als bijvoorbeeld de entiteit voor het adres (A) wordt gedetecteerd, voegt het vinden van het adres (A) gewicht toe aan de voorspelling voor de entiteit voor het verzendadres (B). 

## <a name="best-practices"></a>Aanbevolen procedures
Leer [aanbevolen procedures](luis-concept-best-practices.md).

## <a name="next-steps"></a>Volgende stappen

Zie [Functies toevoegen](luis-how-to-add-features.md) voor meer informatie over het toevoegen van functies aan uw LUIS-app.