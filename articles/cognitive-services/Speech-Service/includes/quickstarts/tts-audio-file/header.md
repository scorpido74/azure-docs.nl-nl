---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400358"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om tekst te converteren naar gesynthesizerde spraak in een audio bestand. De service tekst naar spraak biedt talloze opties voor gesynthesizerde stemmen, onder [tekst-naar-spraak-taal ondersteuning](../../../language-support.md#text-to-speech). Nadat u aan enkele vereisten hebt voldaan, neemt het samen brengen van spraak in een bestand alleen vijf stappen in beslag:
> [!div class="checklist"]
> * Maak een `SpeechConfig` object op basis van uw abonnements sleutel en-regio.
> * Maak een audio configuratie object dat de bevat. WAV-bestands naam.
> * Maak een `SpeechSynthesizer` -object met behulp van de bovenstaande configuratie objecten.
> * Gebruik het `SpeechSynthesizer` -object om uw tekst te converteren naar gesynthesizerde spraak en deze op te slaan in het opgegeven audio bestand.
> * Inspecteer `SpeechSynthesizer` op fouten geretourneerd.
