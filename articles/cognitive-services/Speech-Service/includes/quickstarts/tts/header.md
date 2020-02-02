---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961594"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om tekst naar gesynthesizerde spraak te converteren. De service tekst naar spraak biedt talloze opties voor gesynthesizerde stemmen, onder [tekst-naar-spraak-taal ondersteuning](../../../language-support.md#text-to-speech). Nadat u aan enkele vereisten hebt voldaan, worden in de standaard luidsprekers alleen vier stappen weer gegeven:
> [!div class="checklist"]
> * Een `SpeechConfig`-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een `SpeechSynthesizer`-object met behulp van het `SpeechConfig`-object.
> * Het `SpeechSynthesizer`-object gebruiken om de tekst te spreken.
> * Controleer het `SpeechSynthesisResult` geretourneerd voor fouten.
