---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400380"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om tekst naar gesynthesizerde spraak te converteren. De service tekst naar spraak biedt talloze opties voor gesynthesizerde stemmen, onder [tekst-naar-spraak-taal ondersteuning](../../../language-support.md#text-to-speech). Nadat u aan enkele vereisten hebt voldaan, worden in de standaard luidsprekers alleen vier stappen weer gegeven:
> [!div class="checklist"]
> * Maak een `SpeechConfig` object op basis van uw abonnements sleutel en-regio.
> * Maak een `SpeechSynthesizer` object met behulp van het `SpeechConfig` bovenstaande object.
> * Het `SpeechSynthesizer` object gebruiken om de tekst te spreken.
> * Controleer de `SpeechSynthesisResult` geretourneerde op fouten.
