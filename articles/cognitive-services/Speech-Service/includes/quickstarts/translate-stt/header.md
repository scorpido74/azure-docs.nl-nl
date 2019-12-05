---
title: 'Snelstartgids: Vertaal spraak-naar-tekst-spraak-service'
titleSuffix: Azure Cognitive Services
description: Nader te bepalen
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: cd45657beacd04eb2376af3e4297eae051157778
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816932"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak van de ene taal te vertalen naar tekst in een andere taal. Nadat u hebt voldaan aan enkele vereisten, is het omzetten van spraak naar tekst in vijf stappen vereist:
> [!div class="checklist"]
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Werk het ````SpeechConfig````-object bij om de bron-en doel talen op te geven.
> * Maak een ````TranslationRecognizer````-object met behulp van het ````SpeechConfig````-object.
> * Gebruik het ````TranslationRecognizer````-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde ````TranslationRecognitionResult````.
