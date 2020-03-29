---
title: Inhoudsvertaling voorkomen - Translator Text API
titleSuffix: Azure Cognitive Services
description: Voorkom vertaling van inhoud met de Translator Text API. Met de Translator Text API u inhoud taggen zodat deze niet wordt vertaald.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052490"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Hoe te voorkomen dat de vertaling van inhoud met de Translator Text API

Met de Translator Text API u inhoud taggen zodat deze niet wordt vertaald. U bijvoorbeeld code, een merknaam of een woord/zinswoord taggen die niet zinvol is wanneer deze is gelokaliseerd.

## <a name="methods-for-preventing-translation"></a>Methoden om vertaling te voorkomen

1. Tag uw `notranslate`inhoud met . Het is door het ontwerp dat dit werkt alleen wanneer de invoer textType is ingesteld als HTML

   Voorbeeld:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Gebruik het [dynamische woordenboek](dynamic-dictionary.md) om een specifieke vertaling voor te schrijven.

3. Geef de tekenreeks niet door aan de Vertaaltekst-API voor vertaling.

4. Aangepaste vertaler: Gebruik een [woordenboek in Aangepaste vertaler](custom-translator/what-is-dictionary.md) om de vertaling van een zin met 100% waarschijnlijkheid voor te schrijven.


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Vertaling in uw Translator API-aanroep vermijden](reference/v3-0-translate.md)
