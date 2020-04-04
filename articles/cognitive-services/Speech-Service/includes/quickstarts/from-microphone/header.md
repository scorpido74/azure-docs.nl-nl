---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3775690802c89805ccf9df1ee6d6717a8818213f
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658490"
---
In deze quickstart gebruikt u de [Spraak-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om spraak interactief te herkennen van een microfooningang en de teksttranscriptie te halen uit vastgelegde audio. Het is eenvoudig om deze functie te integreren in uw apps of apparaten voor algemene herkenningstaken, zoals het transcriberen van gesprekken. Het kan ook worden gebruikt voor complexere integraties, zoals het gebruik van het Bot Framework met de Speech SDK om spraakassistenten te bouwen.

Na het voldoen aan een paar voorwaarden, het herkennen van spraak van een microfoon duurt slechts vier stappen:

> [!div class="checklist"]
> * Maak `SpeechConfig` een object op basis van uw abonnementssleutel en regio.
> * Maak `SpeechRecognizer` een object `SpeechConfig` met het object van bovenaf.
> * Start `SpeechRecognizer` het herkenningsproces voor één utterance met het object.
> * Inspecteer de `SpeechRecognitionResult` teruggekeerden.
