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
ms.openlocfilehash: ec39b3692a90f22409e85b5502d3ea874e3282d6
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742057"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Omzetting van inhoud met de vertaler voor komen

Met de vertaler kunt u inhoud labelen zodat deze niet wordt vertaald. U wilt bijvoorbeeld een code, een merknaam of een woord/zin labelen die niet goed kan worden vertaald.

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

2. Voorzie uw inhoud van met `translate="no"` . Dit werkt alleen wanneer de invoer textType is ingesteld als HTML

   Voorbeeld:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>

3. Use the [dynamic dictionary](dynamic-dictionary.md) to prescribe a specific translation.

4. Don't pass the string to the Translator for translation.

5. Custom Translator: Use a [dictionary in Custom Translator](custom-translator/what-is-dictionary.md) to prescribe the translation of a phrase with 100% probability.


## Next steps
> [!div class="nextstepaction"]
> [Use the Translate operation to translate text](reference/v3-0-translate.md)
