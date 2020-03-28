---
title: 'Snelstart: spraak-naar-tekst vertalen - Spraakservice'
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
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74981006"
---
In deze quickstart gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak interactief te vertalen van de ene taal naar tekst in een andere taal. Na het voldoen aan een paar voorwaarden, het vertalen van spraak-naar-tekst duurt slechts vijf stappen:
> [!div class="checklist"]
> * Maak ````SpeechConfig```` een object op basis van uw abonnementssleutel en regio.
> * Werk ````SpeechConfig```` het object bij om de bron- en doeltalen op te geven.
> * Maak ````TranslationRecognizer```` een object ````SpeechConfig```` met het object van bovenaf.
> * Start ````TranslationRecognizer```` het herkenningsproces voor één utterance met het object.
> * Inspecteer de ````TranslationRecognitionResult```` teruggekeerden.
