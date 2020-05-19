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
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 864f6a6d92306c40713f66b526c8a8df1683d3c4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586793"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Filters voor scheld woorden toevoegen met het conversie programma

Normaal gesp roken behoudt de Translator-service woorden die aanwezig zijn in de bron in de vertaling. De mate van scheld woorden en de context die het ongepaste woord is, verschillen per cultuur. Als gevolg hiervan kan de mate van scheld woorden in de doel taal worden versterkt of gereduceerd.

Als u wilt voor komen dat er grove taal in de vertaling wordt weer gegeven, zelfs als scheld woorden in de bron tekst aanwezig zijn, gebruikt u de filter optie voor scheld woorden die beschikbaar is in de methode trans late (). Met deze optie kunt u kiezen of u ongepaste woorden wilt verwijderen, gemarkeerd met de juiste Tags of geen actie ondernomen.

De methode relate () gebruikt de para meter ' Options ', die het nieuwe element ' ProfanityAction ' bevat. De geaccepteerde waarden van ProfanityAction zijn ' geen actie ', ' gemarkeerd ' en ' verwijderd '.

## <a name="accepted-values-of-profanityaction-and-examples"></a>Geaccepteerde waarden van ProfanityAction en voor beelden
|Waarde ProfanityAction | Actie | Voor beeld: bron-Japans | Voor beeld: doel-Engels|
| :---|:---|:---|:---|
| Geen actie | Standaard. Hetzelfde als de optie niet instellen. Gods gang van de bron naar het doel. | 彼は変態です Marketplace. | Hij is een schokkerige. |
| Duidelijke | Ongepaste woorden zijn omgeven door XML-codes \<>... \< /profanity>. | 彼は変態です Marketplace. | Hij is een \< grove>e \< /profanity->. |
| Verwijderd | Ongepaste woorden worden verwijderd uit de uitvoer zonder vervanging. | 彼は Marketplace. | Hij is een. |

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Filters voor scheld woorden Toep assen met uw Vertaal oproep](reference/v3-0-translate.md)
