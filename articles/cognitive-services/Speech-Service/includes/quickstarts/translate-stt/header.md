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
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: c45e75038d1731c933ccf8bf26f9de573e409292
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502784"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak van de ene taal te vertalen naar tekst in een andere taal. Nadat u hebt voldaan aan enkele vereisten, is het omzetten van spraak naar tekst in vijf stappen vereist:
> [!div class="checklist"]
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Werk het ````SpeechConfig````-object bij om de bron-en doel talen op te geven.
> * Maak een ````TranslationRecognizer````-object met behulp van het ````SpeechConfig````-object.
> * Gebruik het ````TranslationRecognizer````-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde ````TranslationRecognitionResult````.
