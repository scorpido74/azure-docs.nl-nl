---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400358"
---
In deze quickstart gebruikt u de [Spraak-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om tekst om te zetten in gesynthetiseerde spraak in een audiobestand. De tekst-naar-spraak service biedt tal van opties voor gesynthetiseerde stemmen, onder [tekst-naar-spraak taal ondersteuning](../../../language-support.md#text-to-speech). Na het voldoen aan een paar voorwaarden, het synthetiseren van spraak in een bestand duurt slechts vijf stappen:
> [!div class="checklist"]
> * Maak `SpeechConfig` een object op basis van uw abonnementssleutel en regio.
> * Maak een object Audioconfiguratie dat de . WAV-bestandsnaam.
> * Maak `SpeechSynthesizer` een object met de configuratieobjecten van bovenaf.
> * Met `SpeechSynthesizer` het object zet u uw tekst om in gesynthetiseerde spraak en slaat u deze op in het opgegeven audiobestand.
> * Controleer `SpeechSynthesizer` de geretourneerde geretourneerde op fouten.
