---
title: Aantal tekens - API voor vertalertekst
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe de Azure Cognitive Services Translator Text API tekens telt, zodat u begrijpen hoe deze inhoud inneemt.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb70b8624ac0d909511032622948f14f8764b153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888149"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Hoe de Translator Text API tekens telt

De Translator Text API telt elk Unicode-codepunt van invoertekst als een teken. Elke vertaling van een tekst naar een taal telt als een afzonderlijke vertaling, zelfs als de aanvraag is gedaan in één API-aanroep die naar meerdere talen wordt vertaald. De lengte van het antwoord doet er niet toe.

Wat telt is:

* Tekst die wordt doorgegeven aan de Api voor vertalertekst in de hoofdtekst van de aanvraag
   * `Text`bij gebruik van de methoden Vertalen, Transliterate en Dictionary
   * `Text`en `Translation` bij gebruik van de methode Woordenboekvoorbeelden
* Alle opmaak: HTML, XML-tags, enz. Json-notatie die wordt gebruikt om de aanvraag te bouwen (bijvoorbeeld 'Tekst:') wordt niet meegeteld.
* Een individuele brief
* Interpunctie
* Een spatie, tabblad, opmaak en elk witspatieteken
* Elk codepunt gedefinieerd in Unicode
* Een herhaalde vertaling, zelfs als u dezelfde tekst eerder hebt vertaald

Voor scripts op basis van ideogrammen zoals Chinees en Japans Kanji telt de Translator Text API nog steeds het aantal Unicode-codepunten, één teken per ideogram. Uitzondering: Unicode surrogaten tellen als twee tekens.

Het aantal aanvragen, woorden, bytes of zinnen is niet relevant in het aantal tekens.

Oproepen naar de methoden Detecteren en BreakSentence worden niet meegeteld in het tekenverbruik. We verwachten echter wel dat de aanroepen naar de methoden Detecteren en BreakSentence in een redelijke verhouding staan tot het gebruik van andere functies die worden geteld. Als het aantal aanroepen detecteren of breaksentence dat u aandoet het aantal andere getelde methoden 100 keer overschrijdt, behoudt Microsoft zich het recht voor om uw gebruik van de methoden Detecteren en breaksentence te beperken.


Meer informatie over het aantal tekens is te vinden in de [veelgestelde vragen over Microsoft Translator.](https://www.microsoft.com/en-us/translator/faq.aspx)
