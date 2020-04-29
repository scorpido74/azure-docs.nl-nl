---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400816"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak van een microfoon invoer interactief te herkennen en de tekst transcriptie van vastgelegde audio op te halen. Het is eenvoudig om deze functie te integreren in uw apps of apparaten voor algemene herkennings taken, zoals het transcriberen van gesp rekken. Het kan ook worden gebruikt voor complexere integraties, zoals het gebruik van het bot-Framework met de Speech SDK om spraak assistenten te bouwen.

Nadat u aan enkele vereisten hebt voldaan, heeft het herkennen van spraak van een microfoon slechts vier stappen:

> [!div class="checklist"]
> * Maak een `SpeechConfig` object op basis van uw abonnements sleutel en-regio.
> * Maak een `SpeechRecognizer` object met behulp van het `SpeechConfig` bovenstaande object.
> * Gebruik het `SpeechRecognizer` -object om het herkennings proces voor één utterance te starten.
> * Inspecteer `SpeechRecognitionResult` de geretourneerde.
