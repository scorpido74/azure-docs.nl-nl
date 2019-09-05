---
title: 'Quickstart: Speech herkennen, C# (UWP)-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel maakt u in C# een UWP-app (Universal Windows Platform) met behulp van de Speech-SDK van Cognitive Services. U gaat in realtime spraak naar tekst transcriberen via de microfoon van uw apparaat. De toepassing is gebouwd met het Speech SDK NuGet-pakket en micro soft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: fe5ff376a7895e2ca5246c0b9eb575752b07c7a1
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382276"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Quickstart: Gesproken tekst herkennen in een UWP-app met behulp van de Speech-SDK

Quick starts zijn ook beschikbaar voor [spraak-synthese](quickstart-text-to-speech-csharp-uwp.md), [spraak omzetting](quickstart-translate-speech-uwp.md)en de [eerste virtuele assistent van Voice](quickstart-virtual-assistant-csharp-uwp.md).

Als u wilt, kiest u een andere programmeer taal en/of-omgeving:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In dit artikel ontwikkelt u een C# universeel Windows-platform-toepassing (UWP) met behulp van de COGNITIVE Services [Speech SDK](speech-sdk.md). Het programma transcribeert spraak naar tekst in realtime van de microfoon van uw apparaat. De toepassing is gebouwd met het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget) en micro soft Visual Studio 2019 (alle edities).

> [!NOTE]
> Het Universal Windows Platform stelt u in staat om apps te ontwikkelen die kunnen worden uitgevoerd op elk apparaat dat ondersteuning biedt voor Windows 10, met inbegrip van pc's, Xbox, Surface Hub en andere apparaten.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Een Azure-abonnementssleutel voor de Spraakservice. [Gratis downloaden](get-started.md).

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

Voeg nu de XAML-code toe die de gebruikers interface van de toepassing definieert en voeg C# de code-behind-implementatie toe.

1. Open`MainPage.xaml`in **Solution Explorer**.

1. In de XAML-weer gave van de ontwerp functie plaatst u het volgende XAML-fragment in `<Grid>` de `</Grid>` **grid** -tag (tussen en):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Open in **Solution Explorer**het bron bestand `MainPage.xaml.cs`voor de code achter. (Deze wordt gegroepeerd onder `MainPage.xaml`.)

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Zoek in de handler van `SpeechRecognitionFromMicrophone_ButtonClicked` het bron bestand de teken `YourSubscriptionKey`reeks en vervang deze door de sleutel van uw abonnement.

1. Zoek de `SpeechRecognitionFromMicrophone_ButtonClicked` teken reeks `YourServiceRegion`in de handler en vervang deze door de [regio](regions.md) die aan uw abonnement is gekoppeld. (Gebruik `westus` bijvoorbeeld voor het gratis proef abonnement.)

1. Kies in de menu balk de optie **bestand** > **Alles opslaan** om uw wijzigingen op te slaan.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

U bent nu klaar om uw toepassing te bouwen en te testen.

1. Kies > build**Build Solution** in de menu balk om de toepassing **te bouwen.** De code moet nu zonder fouten worden gecompileerd.

1. Kies **fout** > **opsporing starten** (of druk op **F5**) om de toepassing te starten. Het venster **HelloWorld** wordt weer gegeven.

   ![Voor beeld van een UWP- C# toepassing voor spraak herkenning in Quick Start](media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Selecteer **microfoon inschakelen**en selecteer wanneer de toegangs machtigings aanvraag wordt weer gegeven, **Ja**.

   ![Verzoek om toegang tot de microfoon](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecteer **Speech recognition with microphone input** en zeg een Engelse woordgroep of zin in de microfoon van uw apparaat. Uw spraak wordt verzonden naar de spraak Services en getranscribeerd naar tekst, die wordt weer gegeven in het venster.

   ![Gebruikers interface voor spraak herkenning](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Snelstart: Spraak omzetten met de Speech SDK voor C# (UWP)](quickstart-translate-speech-uwp.md)
- [Een model trainen voor Custom Speech](how-to-custom-speech-train-model.md)
