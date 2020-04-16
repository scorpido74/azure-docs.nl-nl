---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400816"
---
In deze quickstart gebruikt u de [Spraak-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak interactief te herkennen van een microfooningang en de teksttranscriptie te halen uit vastgelegde audio. Het is eenvoudig om deze functie te integreren in uw apps of apparaten voor algemene herkenningstaken, zoals het transcriberen van gesprekken. Het kan ook worden gebruikt voor complexere integraties, zoals het gebruik van het Bot Framework met de Speech SDK om spraakassistenten te bouwen.

Na het voldoen aan een paar voorwaarden, het herkennen van spraak van een microfoon duurt slechts vier stappen:

> [!div class="checklist"]
> * Maak `SpeechConfig` een object op basis van uw abonnementssleutel en regio.
> * Maak `SpeechRecognizer` een object `SpeechConfig` met het object van bovenaf.
> * Start `SpeechRecognizer` het herkenningsproces voor één utterance met het object.
> * Inspecteer de `SpeechRecognitionResult` teruggekeerden.
