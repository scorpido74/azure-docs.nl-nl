---
title: 'Snelstartgids: Vertaal spraak-naar-tekst-spraak-service'
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74981006"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak van de ene taal te vertalen naar tekst in een andere taal. Nadat u hebt voldaan aan enkele vereisten, is het omzetten van spraak naar tekst in vijf stappen vereist:
> [!div class="checklist"]
> * Maak een ````SpeechConfig```` object op basis van uw abonnements sleutel en-regio.
> * Werk het ````SpeechConfig```` object bij om de bron-en doel talen op te geven.
> * Maak een ````TranslationRecognizer```` object met behulp van het ````SpeechConfig```` bovenstaande object.
> * Gebruik het ````TranslationRecognizer```` -object om het herkennings proces voor één utterance te starten.
> * Inspecteer ````TranslationRecognitionResult```` de geretourneerde.
