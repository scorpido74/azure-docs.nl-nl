---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961399"
---
In deze quickstart gebruikt u de [Spraak-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om tekst om te zetten in gesynthetiseerde spraak in een audiobestand. De tekst-naar-spraak service biedt tal van opties voor gesynthetiseerde stemmen, onder [tekst-naar-spraak taal ondersteuning](../../../language-support.md#text-to-speech). Na het voldoen aan een paar voorwaarden, het synthetiseren van spraak in een bestand duurt slechts vijf stappen:
> [!div class="checklist"]
> * Maak `SpeechConfig` een object op basis van uw abonnementssleutel en regio.
> * Maak een object Audioconfiguratie dat de . WAV-bestandsnaam.
> * Maak `SpeechSynthesizer` een object met de configuratieobjecten van bovenaf.
> * Met `SpeechSynthesizer` het object zet u uw tekst om in gesynthetiseerde spraak en slaat u deze op in het opgegeven audiobestand.
> * Controleer `SpeechSynthesizer` de geretourneerde geretourneerde op fouten.
