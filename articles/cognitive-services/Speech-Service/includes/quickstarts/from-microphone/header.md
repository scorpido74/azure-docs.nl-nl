---
title: 'Snelstart: spraak herkennen vanuit een microfoon - Spraakservice'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75468485"
---
In deze quickstart gebruikt u de [Spraak-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak interactief te herkennen van een microfooningang en de teksttranscriptie te halen uit vastgelegde audio. Het is eenvoudig om deze functie te integreren in uw apps of apparaten voor algemene herkenningstaken, zoals het transcriberen van gesprekken. Het kan ook worden gebruikt voor complexere integraties, zoals het gebruik van het Bot Framework met de Speech SDK om spraakassistenten te bouwen.

Na het voldoen aan een paar voorwaarden, het herkennen van spraak van een microfoon duurt slechts vier stappen:

> [!div class="checklist"]
> * Maak `SpeechConfig` een object op basis van uw abonnementssleutel en regio.
> * Maak `SpeechRecognizer` een object `SpeechConfig` met het object van bovenaf.
> * Start `SpeechRecognizer` het herkenningsproces voor één utterance met het object.
> * Inspecteer de `SpeechRecognitionResult` teruggekeerden.
