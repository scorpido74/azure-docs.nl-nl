---
title: 'Quick Start: spraak herkennen vanuit een audio bestand-spraak service'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: bbd3880ea679dc5fc86c0fc1ece101ca3fca74d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504135"
---
In deze Quick start gaat u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) gebruiken om spraak van een audio bestand te herkennen. Nadat u hebt voldaan aan enkele vereisten, is het herkennen van spraak vanuit een bestand slechts vijf stappen:
> [!div class="checklist"]
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een ````AudioConfig````-object dat de bevat. WAV-bestands naam.
> * Maak een ````SpeechRecognizer````-object met behulp van de bovenstaande ````SpeechConfig```` en ````AudioConfig```` objecten.
> * Gebruik het ````SpeechRecognizer````-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde ````SpeechRecognitionResult````.
