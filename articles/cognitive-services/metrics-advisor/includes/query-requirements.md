---
title: Vereisten voor query's
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 68b5946cb21bef1c8979a94c8780682cfe9defa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377256"
---
Gebruik de parameter `@StartTime` in de query om metrische gegevens voor een bepaald tijdstempel op te halen. Dit wordt vervangen door een tekenreeks in de indeling `yyyy-MM-ddTHH:mm:ss`. 

> [!IMPORTANT]
> Zorg ervoor dat alleen metrische gegevens van **één tijdstempel** door de query worden geretourneerd. Metrics Advisor voert de query uit op elk tijdstempel om de bijbehorende metrische gegevens op te halen. Een query voor een metriek met *dagelijkse* granulatie mag bijvoorbeeld slechts één tijdstempel bevatten, zoals `2020-06-21T00:00:00Z` wanneer de query één keer wordt uitgevoerd. 
