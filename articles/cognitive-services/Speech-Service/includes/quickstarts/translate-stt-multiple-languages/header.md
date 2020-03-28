---
title: 'Snelstart: spraak vertalen naar meerdere talen - Spraakservice'
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
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74981532"
---
In deze quickstart gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak interactief te vertalen van de ene taal naar spraak in een andere taal. Na het voldoen aan een paar voorwaarden, het vertalen van spraak naar tekst in meerdere talen duurt slechts zes stappen:
> [!div class="checklist"]
> * Maak ````SpeechTranslationConfig```` een object op basis van uw abonnementssleutel en regio.
> * Werk ````SpeechTranslationConfig```` het object bij om de brontaal voor spraakherkenning op te geven.
> * Werk ````SpeechTranslationConfig```` het object bij om meerdere vertaaldoeltalen op te geven.
> * Maak ````TranslationRecognizer```` een object ````SpeechTranslationConfig```` met het object van bovenaf.
> * Start ````TranslationRecognizer```` het herkenningsproces voor één utterance met het object.
> * Inspecteer de ````TranslationRecognitionResult```` teruggekeerden.
