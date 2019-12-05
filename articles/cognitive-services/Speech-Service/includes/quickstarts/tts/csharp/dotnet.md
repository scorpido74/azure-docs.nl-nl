---
title: 'Snelstartgids: een synthese van de C# spraak, (Windows)-spraak service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze hand leiding voor het maken van een tekst-naar-spraak-console toepassing met behulp van .NET Framework voor Windows en de spraak-SDK. Wanneer u klaar bent, kunt u spraak van tekst verwerken en de spraak op uw spreker in realtime belui Steren.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 38dac73d253245fce86ac419c7a4eb3a106df3f0
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818131"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open **Program.cs** en vervang de automatisch gegenereerde code door dit voor beeld:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Zoek de teken reeks `YourSubscriptionKey`en vervang deze door de abonnements sleutel van uw speech-service.

1. Zoek de teken reeks `YourServiceRegion`en vervang deze door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. Als u bijvoorbeeld het gratis proef abonnement gebruikt, is de regio `westus`.

1. Kies in de menu balk de optie **bestand** > alles op te **slaan**.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Kies in de menu balk de optie **build** > **Build** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fout opsporing** > **fout opsporing starten** (of selecteer **F5**) om de toepassing **HelloWorld** te starten.

1. Voer een Engelse woord groep of zin in. De toepassing verzendt uw tekst naar de spraak service, waarmee gesynthesizerde spraak naar de toepassing wordt verzonden om op uw spreker af te spelen.

   ![Gebruikers interface voor spraak synthese](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraak voorbeelden vastleggen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
