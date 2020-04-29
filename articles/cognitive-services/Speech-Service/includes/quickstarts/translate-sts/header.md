---
title: 'Snelstartgids: Vertaal spraak-naar-spraak-spraak service'
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74980515"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak herkenning van de ene taal naar de spraak in een andere taal te vertalen. Nadat u aan enkele vereisten hebt voldaan, kunt u spraak naar spraak alleen omzetten in zes stappen:
> [!div class="checklist"]
> * Maak een ````SpeechTranslationConfig```` object op basis van uw abonnements sleutel en-regio.
> * Werk het ````SpeechTranslationConfig```` object bij om de bron-en doel talen op te geven.
> * Werk het ````SpeechTranslationConfig```` object bij om de stem naam voor spraak uitvoer op te geven.
> * Maak een ````TranslationRecognizer```` object met behulp van het ````SpeechTranslationConfig```` bovenstaande object.
> * Gebruik het ````TranslationRecognizer```` -object om het herkennings proces voor één utterance te starten.
> * Inspecteer ````TranslationRecognitionResult```` de geretourneerde.
