---
title: 'Snelstart: spraak herkennen uit een audiobestand - Spraakservice'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76038138"
---
In deze quickstart gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak uit een audiobestand te herkennen. Na het voldoen aan een paar voorwaarden, het herkennen van spraak uit een bestand duurt slechts een paar stappen:
> [!div class="checklist"]
> * Maak `SpeechConfig` een object op basis van uw abonnementssleutel en regio.
> * Maak `AudioConfig` een object dat de . WAV-bestandsnaam.
> * Maak `SpeechRecognizer` een object `SpeechConfig` `AudioConfig` met de en objecten van bovenaf.
> * Start `SpeechRecognizer` het herkenningsproces voor één utterance met het object.
> * Inspecteer de `SpeechRecognitionResult` teruggekeerden.
