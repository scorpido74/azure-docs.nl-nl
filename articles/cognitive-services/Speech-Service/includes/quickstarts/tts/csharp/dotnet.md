---
title: 'Snelstartgids: een synthese van de spraak, C# (Windows)-Speech Service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze hand leiding voor het maken van een tekst-naar-spraak-console toepassing met behulp van .NET Framework voor Windows en de spraak-SDK. Wanneer u klaar bent, kunt u spraak van tekst verwerken en de spraak op uw spreker in realtime belui Steren.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89f491aae91787ec6b67b9aa2daee262f3bab62e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275087"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Stel uw ontwikkel omgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open **Program.cs** en vervang de automatisch gegenereerde code door dit voor beeld:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Zoek de teken `YourSubscriptionKey`reeks en vervang deze door de abonnements sleutel van uw spraak service.

1. Zoek de teken `YourServiceRegion`reeks en vervang deze door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. Als u bijvoorbeeld het gratis proef abonnement gebruikt, is `westus`de regio.

1. Kies in de menu balk de optie **bestand** > **Opslaan**.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. **Kies** > build**Build Solution** in de menu balk om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **debug** > **Start Debugging** (of selecteer **F5**) om de toepassing **HelloWorld** te starten.

1. Voer een Engelse woord groep of zin in. De toepassing verzendt uw tekst naar de spraak service, waarmee gesynthesizerde spraak naar de toepassing wordt verzonden om op uw spreker af te spelen.

   ![Gebruikers interface voor spraak synthese](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraak voorbeelden vastleggen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
