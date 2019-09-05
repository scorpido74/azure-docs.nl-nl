---
title: 'Quickstart: Bewaak spraak, C++ (Windows)-Speech Service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het in-en C++ uitspreken van spraak in het Windows-bureau blad met behulp van de Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 686b21d3e02266af77687778c32f0d1ca6d55154
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383079"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Quickstart: Spraak maken in C++ Windows met behulp van de Speech SDK

Quick starts zijn ook beschikbaar voor [spraak herkenning](quickstart-cpp-windows.md) en [spraak omzetting](quickstart-translate-speech-cpp-windows.md).

In dit artikel maakt u een C++-consoletoepassing voor Windows. U gebruikt de Cognitive Services [Speech SDK](speech-sdk.md) om spraak van tekst in realtime te synthesizeren en de spraak op de spreker van uw PC af te spelen. De toepassing is gebouwd met het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget) en micro soft Visual Studio 2019 (alle edities).

Zie [taal ondersteuning](language-support.md#text-to-speech)voor een volledige lijst met talen/stemmen die beschikbaar zijn voor spraak synthese.

## <a name="prerequisites"></a>Vereisten

U hebt een abonnement op spraak services nodig om deze Quick Start te volt ooien. U kunt er gratis een krijgen. Zie [de spraak services gratis uitproberen](get-started.md) voor meer informatie.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand **helloworld.cpp**.

1. Vervang alle code door het volgende fragment:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Kies in de menu balk de optie **bestand** > **Opslaan**.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Selecteer > in de menu balk build**Build Solution** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **debug** > **Start Debugging** (of druk op **F5**) om de toepassing **HelloWorld** te starten.

1. Typ een Engelse zin of zin. De toepassing verzendt uw tekst naar de spraak Services, waarmee gesynthesizerde spraak naar de toepassing wordt verzonden om op uw spreker af te spelen.

   ![Console-uitvoer na geslaagde spraak-synthese](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Volgende stappen

Aanvullende voor beelden, zoals het opslaan van spraak naar een audio bestand, zijn beschikbaar op GitHub.

> [!div class="nextstepaction"]
> [C++-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](how-to-custom-voice-create-voice.md)
- [Aangepaste spraak voorbeelden vastleggen](record-custom-voice-samples.md)
