---
title: 'Quick Start: C# een UWP-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel maakt u in C# een UWP-app (Universal Windows Platform) met behulp van de Speech-SDK van Cognitive Services. U kunt spraak van tekst in realtime op de spreker van uw apparaat. De toepassing is gebouwd met het Speech SDK NuGet-pakket en micro soft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 276de42f2a37a0e48cd323e285760c00e6db9f32
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818344"
---
> [!NOTE]
> Het Universal Windows Platform stelt u in staat om apps te ontwikkelen die kunnen worden uitgevoerd op elk apparaat dat ondersteuning biedt voor Windows 10, met inbegrip van pc's, Xbox, Surface Hub en andere apparaten.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?tabs=uwp)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

Voeg nu de XAML-code toe die de gebruikers interface van de toepassing definieert en voeg C# de code-behind-implementatie toe.

1. Open `MainPage.xaml`in **Solution Explorer**.

1. In de XAML-weer gave van de ontwerp functie plaatst u het volgende XAML-fragment in de **grid** -tag (tussen `<Grid>` en `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. Open in **Solution Explorer**het bron bestand van de code-behind `MainPage.xaml.cs`. (Deze is gegroepeerd onder `MainPage.xaml`.)

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. Zoek in de handler van het bron bestand `Speak_ButtonClicked` de teken reeks `YourSubscriptionKey`en vervang deze door de sleutel van uw abonnement.

1. Zoek in de handler `Speak_ButtonClicked` de teken reeks `YourServiceRegion`en vervang deze door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. (Gebruik `westus` voor het gratis proef abonnement.)

1. Kies in de menu balk de optie **bestand** > **Alles opslaan** om uw wijzigingen op te slaan.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

U bent nu klaar om uw toepassing te bouwen en te testen.

1. Kies in de menu balk de optie **build** > **Build** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fout opsporing** > **fout opsporing starten** (of druk op **F5**) om de toepassing te starten. Het venster **HelloWorld** wordt weer gegeven.

   ![Voor beeld van een UWP- C# toepassing voor spraak synthese in Quick Start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Voer tekst in het tekstvak in en klik op **spreken**. Uw tekst wordt verzonden naar de speech-service en gesynthesizerd op spraak, die op uw spreker wordt afgespeeld.

    ![Gebruikers interface voor spraak synthese](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraak voorbeelden vastleggen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
