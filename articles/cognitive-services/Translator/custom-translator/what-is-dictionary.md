---
title: Wat is een woorden lijst? -Aangepaste Translator
titleSuffix: Azure Cognitive Services
description: Een woorden lijst is een uitgelijnd document waarin een lijst met zinsdelen of zinnen (en de bijbehorende vertalingen) wordt opgegeven die u altijd wilt laten vertalen met micro soft Translator. Woorden lijsten worden soms ook wel Glossaries of term bases genoemd.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a4aac8afb7974be402ee98bb65c920133d4c118f
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947688"
---
# <a name="what-is-a-dictionary"></a>Wat is een woorden lijst?

Een woorden lijst is een uitgelijnd paar documenten waarin een lijst met zinsdelen of zinnen en de bijbehorende vertalingen wordt opgegeven. Gebruik een woorden lijst in uw training als u wilt dat micro soft Translator altijd alle exemplaren van de bron woord groep of zin moet vertalen met behulp van de vertaling die u in de woorden lijst hebt gegeven. Woorden lijsten worden soms Glossaries of term bases genoemd. U kunt de woorden lijst beschouwen als een brute kracht ' kopiëren en vervangen ' voor alle termen die u vermeldt. Daarnaast bouwt micro soft Custom Translator service samen met een eigen woorden boek voor algemeen gebruik om de kwaliteit van de vertaling te verbeteren. Een door de klant verschafte woorden lijst heeft echter een broncel en zal eerst zoeken naar woorden of zinnen.

Woorden boeken werken alleen voor projecten in taal paren met een volledig ondersteund micro soft General Neural-netwerk model achter de computers. [Bekijk de volledige lijst met talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Woordgroepen lijst
Wanneer u een woordgroepen lijst in uw model hebt opgenomen, wordt een wille keurig woord of zinsdeel omgezet in de manier die u hebt opgegeven. De rest van de zin wordt op de gebruikelijke manier vertaald. U kunt een woordgroepen woordenlijst gebruiken om woord groepen op te geven die niet moeten worden vertaald door dezelfde niet-vertaalde woord groep te bieden in het bron-en doel bestand in de woorden lijst.

## <a name="sentence-dictionary"></a>Woorden lijst voor zinnen
Met de woorden lijst voor zinnen kunt u een exacte doel omzetting voor een bron zin opgeven. Voor een zin die overeenkomt met de woorden lijst, moet de gehele verzonden zin overeenkomen met de vermelding van de bron woordenlijst.  Als slechts een deel van de zin overeenkomt, komt de invoer niet overeen.  Wanneer een overeenkomst wordt gedetecteerd, wordt de doel vermelding van de woorden lijst geretourneerd.

## <a name="dictionary-only-trainings"></a>Alleen-woordenlijst trainingen
U kunt een model trainen met alleen woordenlijst gegevens. Als u dit wilt doen, selecteert u alleen het woordenlijst document (of meerdere woordenlijst documenten) die u wilt opnemen en tikt u op model maken. Aangezien dit een alleen-woordenlijst training is, is er geen minimum aantal trainings zinnen vereist. Het model voert doorgaans veel sneller training uit dan een standaard training.  De resulterende modellen gebruiken de micro soft Baseline-modellen voor vertaling, waarbij de toegevoegde woorden lijsten worden toegevoegd.  Het is niet mogelijk om een test rapport op te halen.

>[!Note]
>Aangepaste vertaler heeft geen zin om woordenlijst bestanden uit te lijnen. het is dus belang rijk dat er een gelijk aantal bron-en doel zinnen/zinnen in uw woordenlijst documenten staat en dat ze nauw keurig zijn uitgelijnd.

## <a name="recommendations"></a>Aanbevelingen

- Woorden boeken zijn geen vervanging voor het trainen van een model met behulp van trainings gegevens. Het wordt aanbevolen om deze te vermijden en het systeem te laten leren van uw trainings gegevens. Als er echter zinnen of samengestelde samen stellingen moeten worden weer gegeven als-is, gebruikt u een woorden lijst.
- De woorden lijst woordgroepen moet spaarzaam worden gebruikt. Houd er dus rekening mee dat wanneer een zin binnen een zin wordt vervangen, de context binnen die zin verloren gaat of beperkt is voor het vertalen van de rest van de zin. Het resultaat is dat als de woord groep of het woord in de zin wordt vertaald volgens de meegeleverde woorden lijst, de algehele Vertaal kwaliteit van de zin vaak in orde is.
- De woorden lijst woordgroepen werkt goed voor samengestelde naam woorden, zoals product namen ("Microsoft SQL Server"), juiste namen ("City of Hamburg") of functies van het product ("Pivot Table"). Deze werkt niet gelijk aan woorden of bijvoegingen, omdat deze doorgaans zeer verbuigd zijn in de bron of in de doel taal. De aanbevolen procedures zijn om te voor komen dat vermeldingen voor de woorden lijst in combi natie met samen woorden worden samengesteld.
- Wanneer u een woorden boek gebruikt, is het hoofdletter gebruik en interpunctie belang rijk. Woordenboek vermeldingen komen alleen overeen met woorden en zinsdelen die hetzelfde hoofdletter gebruik en lees tekens hebben als de vermelding die in de woorden lijst voor komt. In uw vertalingen worden het hoofdletter gebruik en interpunctie weer gegeven die zijn opgenomen in de doel zijde van het woordenlijst bestand. Als voor beeld hebt u een Engels-Spaans-model getraind met een woorden lijst waarin ' Hello ' is opgegeven in het bron bestand dat moet worden vertaald naar ' Buenos Dias ' in het doel bestand. Wanneer u een vertaling aanvraagt van een zin die "Hallo" bevat, zoekt het systeem eerst naar uw woorden lijst en wordt er een overeenkomst ("Hallo") gevonden en wordt ' Buenos Dias ' geretourneerd in de laatste vertaling.
- Als een woord meermaals wordt weer gegeven in een woordenlijst bestand, gebruikt het systeem altijd de laatste vermelding die is opgegeven. De woorden lijst moet daarom niet meerdere vertalingen van hetzelfde woord bevatten.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [richt lijnen voor document indelingen](document-formats-naming-convention.md).
