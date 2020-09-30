---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: e3eac0311b8b24c8c6e1fbf4d8cae55c360a7352
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377237"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een resource voor de Azure-spraakservice maken](../../../../overview.md#try-the-speech-service-for-free)
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand **helloworld.cpp**.

1. Vervang alle code door het volgende codefragment:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de tekenreeks `YourServiceRegion` door de **Regio-id** van de [regio](https://aka.ms/speech/sdkregion) die aan uw abonnement is gekoppeld.

1. Vervang de tekenreeks `whatstheweatherlike.wav` door uw eigen bestandsnaam.

1. Kies in de menubalk **Bestand** > **Alles opslaan** om de wijzigingen op te slaan.

> [!NOTE]
> De Speech-SDK probeert taal standaard te herkennen in en-US. Zie [De brontaal voor spraak-naar-tekst opgeven](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Selecteer in de menubalk **Build** > **Build Solution** om de toepassing te compileren. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Fouten opsporen** > **Foutopsporing starten** (of druk op **F5**) om de toepassing **helloworld** te starten.

1. Uw audiobestand wordt verzonden naar de Speech-service en de eerste uiting in het bestand wordt getranscribeerd naar tekst, die in hetzelfde venster wordt weergegeven.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]