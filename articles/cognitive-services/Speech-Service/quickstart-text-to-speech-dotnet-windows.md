---
title: 'Quickstart: Bewaak spraak, .NET Framework (Windows)-Speech Service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze hand leiding voor het maken van een tekst-naar-spraak-console toepassing met behulp van .NET Framework voor Windows en de spraak-SDK. Wanneer u klaar bent, kunt u spraak van tekst verwerken en de spraak op uw spreker in realtime belui Steren.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 1a411455e4a6dea22e092cdfc8e70ee23b656435
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327452"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Quickstart: Stem de spraak met de Speech SDK voor .NET Framework (Windows)

Quick starts zijn ook beschikbaar voor [spraak herkenning](quickstart-csharp-dotnet-windows.md) en [spraak omzetting](quickstart-translate-speech-dotnetframework-windows.md).

Gebruik deze hand leiding voor het maken van een tekst-naar-spraak-console toepassing met behulp van .NET Framework voor Windows en de spraak-SDK. Wanneer u klaar bent, kunt u spraak van tekst verwerken en de spraak op uw spreker in realtime belui Steren.

Voor een snelle demonstratie (zonder zelf het Visual Studio-project te maken, zoals beschreven in dit artikel), haalt u de nieuwste [Cognitive Services Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) -voor beelden van github.

## <a name="prerequisites"></a>Vereisten

Als u dit project wilt voltooien, hebt u het volgende nodig:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Een abonnementssleutel voor de Speech Service. [Gratis downloaden](get-started.md).
* Er is een spreker (of headset) beschikbaar.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open **Program.cs** en vervang de automatisch gegenereerde code door dit voor beeld:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Zoek de teken reeks `YourSubscriptionKey` en vervang deze door uw abonnements sleutel voor spraak Services.

1. Zoek de teken reeks `YourServiceRegion` en vervang deze door de [regio](regions.md) die aan uw abonnement is gekoppeld. Als u bijvoorbeeld het gratis proef abonnement gebruikt, is de regio `westus`.

1. Kies in de menu balk **bestand** > **Alles opslaan**.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Kies in de menu balk **build** > **Build-oplossing** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fouten opsporen** > **fout opsporing starten** (of selecteer **F5**) om de toepassing **HelloWorld** te starten.

1. Voer een Engelse woord groep of zin in. De toepassing verzendt uw tekst naar de spraak Services, waarmee gesynthesizerde spraak naar de toepassing wordt verzonden om op uw spreker af te spelen.

   ![Gebruikers interface voor spraak synthese](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](how-to-custom-voice-create-voice.md)
- [Aangepaste spraak voorbeelden vastleggen](record-custom-voice-samples.md)
