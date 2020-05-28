---
title: Vertaling van inhoud voor komen-Translator
titleSuffix: Azure Cognitive Services
description: Omzetting van inhoud met het conversie programma voor komen. Met de vertaler kunt u inhoud labelen zodat deze niet wordt vertaald.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 37d2e4c0131569ab50ebf49ff73b6adf7a420713
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996173"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Omzetting van inhoud met de vertaler voor komen

Met de vertaler kunt u inhoud labelen zodat deze niet wordt vertaald. Stel dat u code, een merk naam of een woord/zin wilt labelen die niet zinvol is wanneer deze wordt gelokaliseerd.

## <a name="methods-for-preventing-translation"></a>Methoden voor het voor komen van vertalingen

1. Voorzie uw inhoud van met `notranslate` . Het ontwerp dat dit alleen werkt als de invoer textType is ingesteld als HTML

   Voorbeeld:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Gebruik de [dynamische woorden lijst](dynamic-dictionary.md) om een specifieke vertaling te bepalen.

3. Geef de teken reeks niet door aan de vertaler voor vertaling.

4. Aangepaste vertaler: gebruik een [woorden lijst in het aangepaste conversie programma](custom-translator/what-is-dictionary.md) om de vertaling van een woord groep met een waarschijnlijkheid van 100% te bepalen.


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Vertaling in uw Translator-oproep vermijden](reference/v3-0-translate.md)
