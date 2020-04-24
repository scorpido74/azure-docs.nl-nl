---
title: Gegevens conversie-LUIS
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe uitingen kan worden gewijzigd voordat voor spellingen in Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b2455df87c8eae1a48cb6c8b1381dad85d304bf4
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099237"
---
# <a name="convert-data-format-of-utterances"></a>De gegevens indeling van uitingen converteren
LUIS biedt de volgende conversies van een gebruiker utterance vóór voor spelling '

* Spraak naar tekst met behulp van [Cognitive Services speech](../Speech-Service/overview.md) service.

## <a name="speech-to-text"></a>Spraak-naar-tekst

Spraak naar tekst wordt verschaft als een integratie met LUIS.

### <a name="intent-conversion-concepts"></a>Concepten van intentie conversie
Door de conversie van spraak naar tekst in LUIS kunt u gesp roken uitingen naar een eind punt verzenden en een LUIS-Voorspellings reactie ontvangen. Het proces is een integratie van de [spraak](https://docs.microsoft.com/azure/cognitive-services/Speech) service met Luis. Meer informatie over spraak naar opzet vindt u in een [zelf studie](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Belangrijke vereisten
U hoeft geen **Bing Speech-API** sleutel voor deze integratie te maken. Een **Language Understanding** sleutel die in de Azure Portal is gemaakt, werkt voor deze integratie. Gebruik niet de LUIS-start sleutel.

### <a name="pricing-tier"></a>Prijscategorie
Deze integratie maakt gebruik van een ander [prijs](luis-limits.md#key-limits) model dan gebruikelijk language Understanding prijs categorieën.

### <a name="quota-usage"></a>Quota gebruik
Zie de [belangrijkste limieten](luis-limits.md#key-limits) voor informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens uitpakken](luis-concept-data-extraction.md)

