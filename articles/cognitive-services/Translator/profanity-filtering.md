---
title: Filteren op scheld woorden-Translator
titleSuffix: Azure Cognitive Services
description: Gebruik filters voor scheld woorden om het niveau van de scheld in de tekst in de Azure Cognitive Services Translator te bepalen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7ebfe766e6362a3f62e70db8bf2dcae370aceee3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83996156"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Filters voor scheld woorden toevoegen met het conversie programma

Normaal gesp roken behoudt de Translator-service woorden die aanwezig zijn in de bron in de vertaling. De mate van scheld woorden en de context die het ongepaste woord is, verschillen per cultuur. Als gevolg hiervan kan de mate van scheld woorden in de doel taal worden versterkt of gereduceerd.

Als u wilt voor komen dat er grove taal in de vertaling wordt weer gegeven, zelfs als scheld woorden in de bron tekst aanwezig zijn, gebruikt u de filter optie voor scheld woorden die beschikbaar is in de methode trans late (). Met deze optie kunt u kiezen of u ongepaste woorden wilt verwijderen, gemarkeerd met de juiste Tags of geen actie ondernomen.

De methode relate () gebruikt de para meter ' Options ', die het nieuwe element ' ProfanityAction ' bevat. De geaccepteerde waarden van ProfanityAction zijn ' geen actie ', ' gemarkeerd ' en ' verwijderd '.

## <a name="accepted-values-of-profanityaction-and-examples"></a>Geaccepteerde waarden van ProfanityAction en voor beelden
|Waarde ProfanityAction | Bewerking | Voor beeld: bron-Japans | Voor beeld: doel-Engels|
| :---|:---|:---|:---|
| Geen actie | Standaard. Hetzelfde als de optie niet instellen. Gods gang van de bron naar het doel. | 彼は変態です Marketplace. | Hij is een schokkerige. |
| Duidelijke | Ongepaste woorden worden omgeven door XML-tags \<profanity> ... \</profanity> . | 彼は変態です Marketplace. | Hij is een \<profanity> schokkerige \</profanity> . |
| Verwijderd | Ongepaste woorden worden verwijderd uit de uitvoer zonder vervanging. | 彼は Marketplace. | Hij is een. |

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Filters voor scheld woorden Toep assen met uw Vertaal oproep](reference/v3-0-translate.md)
