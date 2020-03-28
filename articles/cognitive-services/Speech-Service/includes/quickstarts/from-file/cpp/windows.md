---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 2f029725ff53c6bf01481f7a188e070cf84550c9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79081962"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=windows)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand **helloworld.cpp**.

1. Vervang alle code door het volgende fragment:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de `YourServiceRegion` tekenreeks door de **regio-id** van [regio](https://aka.ms/speech/sdkregion) die is gekoppeld aan uw abonnement (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Vervang de `whatstheweatherlike.wav` tekenreeks door uw eigen bestandsnaam.

1. Kies Alles**opslaan** **op** > de menubalk .

> [!NOTE]
> De Spraak-SDK wordt standaard herkend door het gebruik van en-ons voor de taal, zie [Brontaal opgeven voor spraak naar tekst](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Selecteer op de menubalk **Build** > **Build Solution** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Foutopsporing debuggen** > **(of** druk op **F5)** om de **helloworld-toepassing** te starten.

1. Uw audiobestand wordt verzonden naar de spraakservice en de eerste uiting in het bestand wordt getranscribeerd naar tekst, die in hetzelfde venster wordt weergegeven.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]