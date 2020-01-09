---
title: 'Quick Start: spraak herkennen vanuit een audio bestand-spraak service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: e333b156eaf9c4b6e09e631513ea099018f0691b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467281"
---
In deze Quick start gaat u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) gebruiken om spraak van een audio bestand te herkennen. Nadat u hebt voldaan aan enkele vereisten, is het herkennen van spraak vanuit een bestand slechts vijf stappen:
> [!div class="checklist"]
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een ````AudioConfig````-object dat de bevat. WAV-bestands naam.
> * Maak een ````SpeechRecognizer````-object met behulp van de bovenstaande ````SpeechConfig```` en ````AudioConfig```` objecten.
> * Gebruik het ````SpeechRecognizer````-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde ````SpeechRecognitionResult````.
