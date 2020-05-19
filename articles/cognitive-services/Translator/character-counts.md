---
title: Aantal tekens-Translator
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe de Azure Cognitive Services Translator tekens telt, zodat u kunt begrijpen hoe inhoud wordt opgenomen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 0219940a4ac60e4a6187d13802c36e8bff1925b3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587320"
---
# <a name="how-the-translator-counts-characters"></a>Het aantal tekens in het conversie programma

De vertaler telt elk Unicode-code punt van invoer tekst als een teken. Elke vertaling van een tekst naar een taal telt als een afzonderlijke vertaling, zelfs als de aanvraag is gedaan in één API-aanroep die naar meerdere talen vertaalt. De lengte van het antwoord is niet van belang.

Wat is het aantal:

* Tekst die wordt door gegeven aan de vertaler in de hoofd tekst van de aanvraag
   * `Text`bij gebruik van de opzoek methoden vertalen, trans actie en Dictionary
   * `Text`en `Translation` Wanneer u de voor beelden van de woordenlijst methode gebruikt
* Alle aantekeningen: HTML, XML-tags enzovoort, in het tekst veld van de hoofd tekst van de aanvraag. De JSON-notatie die wordt gebruikt om de aanvraag te maken (bijvoorbeeld ' Text: '), wordt niet meegerekend.
* Een afzonderlijke brief
* Interpunctie
* Een spatie, tab, opmaak en elk soort spatie teken
* Elk code punt dat in Unicode is gedefinieerd
* Een herhaalde vertaling, zelfs als u dezelfde tekst eerder hebt vertaald

Voor scripts die zijn gebaseerd op ideograms zoals Chinees en Japans Kanji, telt de Translator-service nog steeds het aantal Unicode-code punten, één teken per Ideogram. Uitzonde ring: Unicode-vervangingen tellen als twee tekens.

Het aantal aanvragen, woorden, bytes of zinnen is irrelevant in het aantal tekens.

Aanroepen naar de methoden detect en BreakSentence worden niet meegerekend in het teken gebruik. We verwachten echter dat de aanroepen naar de methoden detect en BreakSentence een redelijk deel uitmaken van het gebruik van andere functies die worden geteld. Als het aantal gedetecteerde of BreakSentence-aanroepen dat u maakt, groter is dan het aantal andere methoden dat door 100 maal is geteld, behoudt micro soft het recht om uw gebruik van de methoden detect en BreakSentence te beperken.

Meer informatie over teken tellingen vindt u in de [Veelgestelde vragen over translators](https://www.microsoft.com/en-us/translator/faq.aspx).
