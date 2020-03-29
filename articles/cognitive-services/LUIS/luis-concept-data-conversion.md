---
title: Gegevensconversie - LUIS
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe uitingen kunnen worden gewijzigd voordat voorspellingen worden gedaan in Taalbegrip (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: aeb9094db83b14af988f70485788934a7854200c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68619806"
---
# <a name="convert-data-format-of-utterances"></a>Gegevensindeling van uitingen converteren
LUIS biedt de volgende conversies van een gebruiker uiting voor voorspelling"

* Spraak naar tekst met behulp van [Cognitive Services Speech](../Speech-Service/overview.md) service. 

## <a name="speech-to-text"></a>Spraak-naar-tekst

Spraak naar tekst wordt geleverd als een integratie met LUIS. 

### <a name="intent-conversion-concepts"></a>Intentieconversieconcepten
Conversie van spraak naar tekst in LUIS stelt u in staat om gesproken uitingen naar een eindpunt te verzenden en een LUIS-voorspellingsantwoord te ontvangen. Het proces is een integratie van de [Spraakservice](https://docs.microsoft.com/azure/cognitive-services/Speech) met LUIS. Meer informatie over Spraak tot intentie met een [zelfstudie](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Belangrijke vereisten
U hoeft geen **Bing Speech API-sleutel** te maken voor deze integratie. Een **taalinformatiesleutel** die is gemaakt in de Azure-portal werkt voor deze integratie. Gebruik de LUIS-startsleutel niet.

### <a name="pricing-tier"></a>Prijscategorie
Deze integratie maakt gebruik van een ander [prijsmodel](luis-boundaries.md#key-limits) dan de gebruikelijke prijsniveaus voor taalbegrip. 

### <a name="quota-usage"></a>Quotumgebruik
Zie [Belangrijke limieten](luis-boundaries.md#key-limits) voor informatie. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens uitpakken](luis-concept-data-extraction.md)

