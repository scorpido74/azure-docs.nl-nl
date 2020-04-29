---
title: 'Snelstartgids: spraak vertalen naar meerdere talen-spraak service'
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74981532"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak herkenning van de ene taal naar de spraak in een andere taal te vertalen. Nadat u aan enkele vereisten hebt voldaan, is het omzetten van spraak naar tekst in meerdere talen alleen zes stappen nodig:
> [!div class="checklist"]
> * Maak een ````SpeechTranslationConfig```` object op basis van uw abonnements sleutel en-regio.
> * Werk het ````SpeechTranslationConfig```` object bij om de bron taal voor spraak herkenning op te geven.
> * Werk het ````SpeechTranslationConfig```` object bij om meerdere Vertaal doel talen op te geven.
> * Maak een ````TranslationRecognizer```` object met behulp van het ````SpeechTranslationConfig```` bovenstaande object.
> * Gebruik het ````TranslationRecognizer```` -object om het herkennings proces voor één utterance te starten.
> * Inspecteer ````TranslationRecognitionResult```` de geretourneerde.
