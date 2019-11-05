---
title: 'Quick Start: spraak herkennen vanuit een microfoon-Speech-Service'
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
ms.openlocfilehash: 64f084f58ae4b12d92d6dee343a59dd56b1c351e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503505"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor het interactief herkennen van spraak van audio gegevens die zijn vastgelegd van een microfoon. Nadat u aan enkele vereisten hebt voldaan, heeft het herkennen van spraak van een microfoon slechts vier stappen:
> [!div class="checklist"]
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een ````SpeechRecognizer````-object met behulp van het ````SpeechConfig````-object.
> * Gebruik het ````SpeechRecognizer````-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde ````SpeechRecognitionResult````.
