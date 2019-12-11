---
title: 'Snelstartgids: spraak vertalen naar meerdere talen-spraak service'
titleSuffix: Azure Cognitive Services
description: Nader te bepalen
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981532"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak herkenning van de ene taal naar de spraak in een andere taal te vertalen. Nadat u aan enkele vereisten hebt voldaan, is het omzetten van spraak naar tekst in meerdere talen alleen zes stappen nodig:
> [!div class="checklist"]
> * Een ````SpeechTranslationConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Werk het ````SpeechTranslationConfig````-object bij om de bron taal voor spraak herkenning op te geven.
> * Werk het ````SpeechTranslationConfig````-object bij om meerdere talen voor vertaal doelen op te geven.
> * Maak een ````TranslationRecognizer````-object met behulp van het ````SpeechTranslationConfig````-object.
> * Gebruik het ````TranslationRecognizer````-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde ````TranslationRecognitionResult````.
