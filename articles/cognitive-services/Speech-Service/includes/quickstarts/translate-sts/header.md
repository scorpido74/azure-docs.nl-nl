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
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 77b060a5caf9865a1296cd6644cf0c51e3253a0e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502875"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak herkenning van de ene taal naar de spraak in een andere taal te vertalen. Nadat u aan enkele vereisten hebt voldaan, kunt u spraak naar spraak alleen omzetten in zes stappen:
> [!div class="checklist"]
> * Een ````SpeechTranslationConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Werk het ````SpeechTranslationConfig````-object bij om de bron-en doel talen op te geven.
> * Werk het ````SpeechTranslationConfig````-object bij om de stem naam voor spraak uitvoer op te geven.
> * Maak een ````TranslationRecognizer````-object met behulp van het ````SpeechTranslationConfig````-object.
> * Gebruik het ````TranslationRecognizer````-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde ````TranslationRecognitionResult````.
