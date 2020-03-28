---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961594"
---
In deze quickstart gebruikt u de [Spraak-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om tekst om te zetten in gesynthetiseerde spraak. De tekst-naar-spraak service biedt tal van opties voor gesynthetiseerde stemmen, onder [tekst-naar-spraak taal ondersteuning](../../../language-support.md#text-to-speech). Na het voldoen aan een paar voorwaarden, het renderen van gesynthetiseerde spraak naar de standaard luidsprekers duurt slechts vier stappen:
> [!div class="checklist"]
> * Maak `SpeechConfig` een object op basis van uw abonnementssleutel en regio.
> * Maak `SpeechSynthesizer` een object `SpeechConfig` met het object van bovenaf.
> * Met `SpeechSynthesizer` behulp van het object om de tekst te spreken.
> * Controleer `SpeechSynthesisResult` de geretourneerde op fouten.
