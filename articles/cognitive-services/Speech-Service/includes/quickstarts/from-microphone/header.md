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
ms.date: 11/07/2019
ms.author: erhopf
ms.openlocfilehash: f3f28cdd8c3c77a5cde2ead86c49b41d54bab5d7
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961179"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor het interactief herkennen van spraak van audio gegevens die zijn vastgelegd van een microfoon. Nadat u aan enkele vereisten hebt voldaan, heeft het herkennen van spraak van een microfoon slechts vier stappen:

> [!div class="checklist"]
> * Een `SpeechConfig`-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een `SpeechRecognizer`-object met behulp van het `SpeechConfig`-object.
> * Gebruik het `SpeechRecognizer`-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde `SpeechRecognitionResult`.
