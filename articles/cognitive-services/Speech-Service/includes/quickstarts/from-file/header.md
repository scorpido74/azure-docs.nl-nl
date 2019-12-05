---
title: 'Quick Start: spraak herkennen vanuit een audio bestand-spraak service'
titleSuffix: Azure Cognitive Services
description: Nader te bepalen
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 2f7ec24667514fb131af29321a53f97210e58fc9
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819426"
---
In deze Quick start gaat u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) gebruiken om spraak van een audio bestand te herkennen. Nadat u hebt voldaan aan enkele vereisten, is het herkennen van spraak vanuit een bestand slechts vijf stappen:
> [!div class="checklist"]
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een ````AudioConfig````-object dat de bevat. WAV-bestands naam.
> * Maak een ````SpeechRecognizer````-object met behulp van de bovenstaande ````SpeechConfig```` en ````AudioConfig```` objecten.
> * Gebruik het ````SpeechRecognizer````-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde ````SpeechRecognitionResult````.
