---
title: 'Quick Start: spraak herkennen vanuit een audio bestand-spraak service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: trbye
ms.openlocfilehash: a7c91775411f100a92dfcb0a7199dd4b25f6eca4
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400565"
---
In deze Quick start gaat u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) gebruiken om spraak van een audio bestand te herkennen. Nadat u aan enkele vereisten hebt voldaan, is het herkennen van spraak van een bestand slechts een paar stappen:
> [!div class="checklist"]
> * Maak een `SpeechConfig` object op basis van uw abonnements sleutel en-regio.
> * Maak een `AudioConfig` -object dat de bevat. WAV-bestands naam.
> * Maak een `SpeechRecognizer` object met behulp van de `SpeechConfig` -en `AudioConfig` -objecten.
> * Gebruik het `SpeechRecognizer` -object om het herkennings proces voor één utterance te starten.
> * Inspecteer `SpeechRecognitionResult` de geretourneerde.
