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
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 6c65fd9a504b5f399afa99280ca2a75b476c750c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502812"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak herkenning van de ene taal naar de spraak in een andere taal te vertalen. Nadat u aan enkele vereisten hebt voldaan, is het omzetten van spraak naar tekst in meerdere talen alleen zes stappen nodig:
> [!div class="checklist"]
> * Een ````SpeechTranslationConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Werk het ````SpeechTranslationConfig````-object bij om de bron taal voor spraak herkenning op te geven.
> * Werk het ````SpeechTranslationConfig````-object bij om meerdere talen voor vertaal doelen op te geven.
> * Maak een ````TranslationRecognizer````-object met behulp van het ````SpeechTranslationConfig````-object.
> * Gebruik het ````TranslationRecognizer````-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde ````TranslationRecognitionResult````.
