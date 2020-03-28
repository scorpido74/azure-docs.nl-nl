---
title: 'Snelstart: Spraak-naar-spraak vertalen - Spraakservice'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980515"
---
In deze quickstart gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak interactief te vertalen van de ene taal naar spraak in een andere taal. Na het voldoen aan een paar voorwaarden, het vertalen van spraak-naar-spraak duurt slechts zes stappen:
> [!div class="checklist"]
> * Maak ````SpeechTranslationConfig```` een object op basis van uw abonnementssleutel en regio.
> * Werk ````SpeechTranslationConfig```` het object bij om de bron- en doeltalen op te geven.
> * Werk ````SpeechTranslationConfig```` het object bij om de spraakuitvoernaam van spraak uitvoer op te geven.
> * Maak ````TranslationRecognizer```` een object ````SpeechTranslationConfig```` met het object van bovenaf.
> * Start ````TranslationRecognizer```` het herkenningsproces voor één utterance met het object.
> * Inspecteer de ````TranslationRecognitionResult```` teruggekeerden.
