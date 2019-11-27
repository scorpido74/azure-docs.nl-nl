---
title: 'Quick Start: spraak herkennen vanuit een microfoon-Speech-Service'
titleSuffix: Azure Cognitive Services
description: Nader te bepalen
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: bee3b64ece3faac6258fe4d017f12833b12e370d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536359"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak op een microfoon invoer interactief te herkennen en de tekst transcriptie van vastgelegde audio op te halen. Het is eenvoudig om deze functie te integreren in uw apps of apparaten voor algemene herkennings taken, zoals het transcriberen van gesp rekken. Het kan ook worden gebruikt voor complexere integraties, zoals het gebruik van het bot-Framework met de Speech SDK om spraak assistenten te bouwen.

Nadat u aan enkele vereisten hebt voldaan, heeft het herkennen van spraak van een microfoon slechts vier stappen:

> [!div class="checklist"]
> * Een `SpeechConfig`-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een `SpeechRecognizer`-object met behulp van het `SpeechConfig`-object.
> * Gebruik het `SpeechRecognizer`-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde `SpeechRecognitionResult`.
