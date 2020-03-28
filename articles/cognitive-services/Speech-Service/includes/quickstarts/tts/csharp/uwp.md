---
title: 'Snelstart: Spraak synthetiseren, C# (UWP) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: In dit artikel maakt u in C# een UWP-app (Universal Windows Platform) met behulp van de Speech-SDK van Cognitive Services. U synthetiseert spraak uit tekst in realtime naar de luidspreker van uw apparaat. De applicatie is gebouwd met het Speech SDK NuGet Package en Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 8ca97be2863bd8e45ac7937c49c464fa2f216b11
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925590"
---
> [!NOTE]
> Het Universal Windows Platform stelt u in staat om apps te ontwikkelen die kunnen worden uitgevoerd op elk apparaat dat ondersteuning biedt voor Windows 10, met inbegrip van pc's, Xbox, Surface Hub en andere apparaten.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

Voeg nu de XAML-code toe die de gebruikersinterface van de toepassing definieert en voeg de C#-code-behind-implementatie toe.

1. Open **in** `MainPage.xaml`Solution Explorer .

1. Plaats in de XAML-weergave van de ontwerper het volgende XAML-fragment in de **rastertag** (tussen `<Grid>` en): `</Grid>`

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. Open **in Solution Explorer**het broncodebestand `MainPage.xaml.cs`met code achter . (Het is gegroepeerd `MainPage.xaml`onder .)

1. Vervang alle code daarin door het volgende fragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. Zoek in de `Speak_ButtonClicked` handler van het `YourSubscriptionKey`bronbestand de tekenreeks en vervang deze door uw abonnementssleutel.

1. Zoek `Speak_ButtonClicked` in de handler `YourServiceRegion`de tekenreeks en vervang deze door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. (Gebruik bijvoorbeeld `westus` voor het gratis proefabonnement.)

1. Kies Alles opslaan **op** > de menubalk om**de** wijzigingen op te slaan.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

Nu bent u klaar om uw toepassing te bouwen en te testen.

1. Kies**build-oplossing** **bouwen** > om de toepassing te bouwen op de menubalk. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Foutopsporing debuggen** > **debuggen** (of druk op **F5)** om de toepassing te starten. Het **helloworld** venster verschijnt.

   ![Voorbeeld van UWP-spraaksynthesetoepassing in C# - snel starten](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Voer tekst in het tekstvak in en klik op **Spreken**. Uw tekst wordt verzonden naar de spraakservice en gesynthetiseerd naar spraak, die wordt afgespeeld op uw luidspreker.

    ![Gebruikersinterface voor spraaksynthese](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraakvoorbeelden opnemen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
