---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961399"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om tekst te converteren naar gesynthesizerde spraak in een audio bestand. De service tekst naar spraak biedt talloze opties voor gesynthesizerde stemmen, onder [tekst-naar-spraak-taal ondersteuning](../../../language-support.md#text-to-speech). Nadat u aan enkele vereisten hebt voldaan, neemt het samen brengen van spraak in een bestand alleen vijf stappen in beslag:
> [!div class="checklist"]
> * Een `SpeechConfig`-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een audio configuratie object dat de bevat. WAV-bestands naam.
> * Maak een `SpeechSynthesizer`-object met behulp van de bovenstaande configuratie objecten.
> * Gebruik het `SpeechSynthesizer`-object om uw tekst te converteren naar gesynthesizerde spraak en deze op te slaan in het opgegeven audio bestand.
> * Inspecteer de `SpeechSynthesizer` geretourneerd voor fouten.
