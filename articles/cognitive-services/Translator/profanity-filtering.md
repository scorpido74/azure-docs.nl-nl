---
title: Godslastering filteren - Translator Text API
titleSuffix: Azure Cognitive Services
description: Gebruik godslasteringsfiltering om het niveau van godslastering te bepalen dat in uw tekst wordt vertaald in de Azure Cognitive Services Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836232"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Filtering toevoegen met de Translator Text API

Normaal gesproken behoudt de Translator-service godslastering die aanwezig is in de bron in de vertaling. De mate van godslastering en de context die woorden profane verschillen tussen culturen. Als gevolg hiervan kan de mate van godslastering in de doeltaal worden versterkt of verminderd.

Als u wilt voorkomen dat u godslastering in de vertaling ziet, zelfs als er godslastering aanwezig is in de brontekst, gebruikt u de optie voor het filteren van godslastering die beschikbaar is in de methode Vertalen(). Met deze optie u kiezen of u godslastering wilt verwijderen, gemarkeerd met de juiste tags of geen actie wilt ondernemen.

De methode Translate() neemt de parameter "opties", die het nieuwe element "ProfanityAction" bevat. De geaccepteerde waarden van ProfanityAction zijn 'NoAction', 'Gemarkeerd' en 'Verwijderd'.

## <a name="accepted-values-of-profanityaction-and-examples"></a>Geaccepteerde waarden van ProfanityAction en voorbeelden
|Waarde van godslasteringActie | Actie | Voorbeeld: Bron - Japans | Voorbeeld: Target - Engels|
| :---|:---|:---|:---|
| NoAction (NoAction) | Standaard. Hetzelfde als het niet instellen van de optie. Godslastering gaat van bron naar doel. | 彼は変態です。 | Hij is een eikel. |
| Gemarkeerd | Profane woorden worden omringd \<door XML-tags godslastering> ... \</godslastering>. | 彼は変態です。 | Hij is \<een godslastering\<>eikel / godslastering>. |
| Verwijderen | Profane woorden worden verwijderd uit de output zonder vervanging. | 彼は。 | Hij is een. |

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Godslasteringfilteren toepassen met uw Translator API-aanroep](reference/v3-0-translate.md)
