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
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80052490"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Omzetting van inhoud met de Translator Text-API voor komen

Met de Translator Text-API kunt u inhoud labelen zodat deze niet wordt vertaald. Stel dat u code, een merk naam of een woord/zin wilt labelen die niet zinvol is wanneer deze wordt gelokaliseerd.

## <a name="methods-for-preventing-translation"></a>Methoden voor het voor komen van vertalingen

1. Voorzie uw inhoud van `notranslate`met. Het ontwerp dat dit alleen werkt als de invoer textType is ingesteld als HTML

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

3. Geef de teken reeks niet door aan de Translator Text-API voor vertaling.

4. Aangepaste vertaler: gebruik een [woorden lijst in het aangepaste conversie programma](custom-translator/what-is-dictionary.md) om de vertaling van een woord groep met een waarschijnlijkheid van 100% te bepalen.


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Vertaling in de API-aanroep van Translator vermijden](reference/v3-0-translate.md)
