---
title: 'Snelstart: spraak herkennen uit een audiobestand - Spraakservice'
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400565"
---
In deze quickstart gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak uit een audiobestand te herkennen. Na het voldoen aan een paar voorwaarden, het herkennen van spraak uit een bestand duurt slechts een paar stappen:
> [!div class="checklist"]
> * Maak `SpeechConfig` een object op basis van uw abonnementssleutel en regio.
> * Maak `AudioConfig` een object dat de . WAV-bestandsnaam.
> * Maak `SpeechRecognizer` een object `SpeechConfig` `AudioConfig` met de en objecten van bovenaf.
> * Start `SpeechRecognizer` het herkenningsproces voor één utterance met het object.
> * Inspecteer de `SpeechRecognitionResult` teruggekeerden.
