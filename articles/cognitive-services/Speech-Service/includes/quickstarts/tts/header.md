---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400380"
---
In deze quickstart gebruikt u de [Spraak-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om tekst om te zetten in gesynthetiseerde spraak. De tekst-naar-spraak service biedt tal van opties voor gesynthetiseerde stemmen, onder [tekst-naar-spraak taal ondersteuning](../../../language-support.md#text-to-speech). Na het voldoen aan een paar voorwaarden, het renderen van gesynthetiseerde spraak naar de standaard luidsprekers duurt slechts vier stappen:
> [!div class="checklist"]
> * Maak `SpeechConfig` een object op basis van uw abonnementssleutel en regio.
> * Maak `SpeechSynthesizer` een object `SpeechConfig` met het object van bovenaf.
> * Met `SpeechSynthesizer` behulp van het object om de tekst te spreken.
> * Controleer `SpeechSynthesisResult` de geretourneerde op fouten.
