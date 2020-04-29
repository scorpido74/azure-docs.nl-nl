---
title: Filteren op scheld woorden-Translator Text-API
titleSuffix: Azure Cognitive Services
description: Gebruik filters voor scheld woorden om het niveau van de scheld in de tekst in de Azure Cognitive Services Translator text-API te bepalen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73836232"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Filters voor scheld woorden toevoegen met de Translator Text-API

Normaal gesp roken behoudt de Translator-service woorden die aanwezig zijn in de bron in de vertaling. De mate van scheld woorden en de context die het ongepaste woord is, verschillen per cultuur. Als gevolg hiervan kan de mate van scheld woorden in de doel taal worden versterkt of gereduceerd.

Als u wilt voor komen dat er grove taal in de vertaling wordt weer gegeven, zelfs als scheld woorden in de bron tekst aanwezig zijn, gebruikt u de filter optie voor scheld woorden die beschikbaar is in de methode trans late (). Met deze optie kunt u kiezen of u ongepaste woorden wilt verwijderen, gemarkeerd met de juiste Tags of geen actie ondernomen.

De methode relate () gebruikt de para meter ' Options ', die het nieuwe element ' ProfanityAction ' bevat. De geaccepteerde waarden van ProfanityAction zijn ' geen actie ', ' gemarkeerd ' en ' verwijderd '.

## <a name="accepted-values-of-profanityaction-and-examples"></a>Geaccepteerde waarden van ProfanityAction en voor beelden
|Waarde ProfanityAction | Bewerking | Voor beeld: bron-Japans | Voor beeld: doel-Engels|
| :---|:---|:---|:---|
| Geen actie | Standaard. Hetzelfde als de optie niet instellen. Gods gang van de bron naar het doel. | 彼は変態です Marketplace. | Hij is een schokkerige. |
| Duidelijke | Ongepaste woorden zijn omgeven door XML \<-codes>... \</profanity>. | 彼は変態です Marketplace. | Hij is een \<Grove>e\</profanity->. |
| Verwijderen | Ongepaste woorden worden verwijderd uit de uitvoer zonder vervanging. | 彼は Marketplace. | Hij is een. |

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Filters voor scheld woorden Toep assen met de API-aanroep van Translator](reference/v3-0-translate.md)
