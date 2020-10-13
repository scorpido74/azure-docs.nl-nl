---
title: Vereisten voor query's
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: 518865f78c170f1fbe4e65b96dc149c1b449a88b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631359"
---
Gebruik de parameter `@StartTime` in de query om metrische gegevens voor een enkele tijdstempel op te halen. Met Metrics Advisor wordt de parameter vervangen door een tekenreeks `yyyy-MM-ddTHH:mm:ss` wanneer de query wordt uitgevoerd.

> [!IMPORTANT]
> Met de query wordt maximaal één record per dimensiecombinatie geretourneerd voor elke tijdstempel. En alle records die zijn geretourneerd met de query, moeten dezelfde tijdstempels hebben. In Metrics Advisor wordt deze query uitgevoerd voor elke tijdstempel om uw gegevens op te nemen. Raadpleeg de sectie [Veelgestelde vragen over query's](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) voor meer informatie en voorbeelden. 