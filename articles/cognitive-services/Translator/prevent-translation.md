---
title: Vertaling van inhoud voor komen-Translator Text-API
titleSuffix: Azure Cognitive Services
description: Omzetting van inhoud met de Translator Text-API voor komen. Met de Translator Text-API kunt u inhoud labelen zodat deze niet wordt vertaald.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: swmachan
ms.openlocfilehash: 15a36451c18d65df6667f24284f3f69f3d1c06b8
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326764"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Omzetting van inhoud met de Translator Text-API voor komen

Met de Translator Text-API kunt u inhoud labelen zodat deze niet wordt vertaald. Stel dat u code, een merk naam of een woord/zin wilt labelen die niet zinvol is wanneer deze wordt gelokaliseerd.

## <a name="methods-for-preventing-translation"></a>Methoden voor het voor komen van vertalingen
1. Escape naar een Twitter-tag @somethingtopassthrough of #somethingtopassthrough. Escape na omzetting ongedaan maken. Dit is de reguliere expressie voor geldige Twitter-Labels: `\B@[A-Za-z]+[A-Za-z0-9_]+)`. Een tag moet beginnen met een @-teken, gevolgd door een teken en vervolgens gevolgd door een of meer tekens, cijfers of een liggend streepje. Het is raadzaam Tags kort te blijven en de openings label moet worden voorafgegaan door een spatie.

2. Label uw inhoud met `notranslate`. Het ontwerp dat dit alleen werkt als de invoer textType is ingesteld als HTML

   Voorbeeld:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Gebruik de [dynamische woorden lijst](dynamic-dictionary.md) om een specifieke vertaling te bepalen.

4. Geef de teken reeks niet door aan de Translator Text-API voor vertaling.

5. Aangepaste vertaler: gebruik een [woorden lijst in het aangepaste conversie programma](custom-translator/what-is-dictionary.md) om de vertaling van een woord groep met een waarschijnlijkheid van 100% te bepalen.


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Vertaling in de API-aanroep van Translator vermijden](reference/v3-0-translate.md)
