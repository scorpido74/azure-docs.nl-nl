---
title: 'Quickstart: Vertaal spraak, C# (UWP)-Speech Service'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start maakt u een Universeel Windows-platform (UWP)-toepassing voor het vastleggen van gebruikers spraak, het vertalen ervan naar een andere taal en het uitvoeren van de tekst naar de opdracht regel. Deze handleiding is bedoeld voor Windows-gebruikers.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 08/19/2019
ms.author: erhopf
ms.topic: quickstart
ms.openlocfilehash: e513cbbc615965ef196a830351aab8ac241c3f20
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382615"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Quickstart: Spraak omzetten met de Speech SDK voor C# (UWP)

Quick starts zijn ook beschikbaar voor [spraak herkenning](quickstart-csharp-uwp.md), [spraak synthese](quickstart-text-to-speech-csharp-uwp.md)en de [eerste virtuele assistent met stem](quickstart-virtual-assistant-csharp-uwp.md).

In deze Quick Start maakt u een Universeel Windows-platform-toepassing (UWP) die de gebruikers spraak van de microfoon van uw computer vastlegt, de spraak vertaalt en de omgezette tekst in realtime overschakelt naar de opdracht regel. Deze toepassing is ontworpen om te worden uitgevoerd op 64-bits Windows en is gebouwd met het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget) en micro soft Visual Studio 2019.

Zie [Taalondersteuning](language-support.md) voor een volledige lijst met talen die voor spraakomzetting beschikbaar zijn.

> [!NOTE]
> UWP stelt u in staat om apps te ontwikkelen die kunnen worden uitgevoerd op elk apparaat dat ondersteuning biedt voor Windows 10, met inbegrip van pc's, Xbox, Surface Hub en andere apparaten.

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Open in **Solution Explorer**het bron bestand `MainPage.xaml.cs`voor de code achter. (Deze wordt gegroepeerd onder `MainPage.xaml`.)

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Zoek in `SpeechTranslationFromMicrophone_ButtonClicked` de handler in dit bestand naar de teken `YourSubscriptionKey`reeks en vervang deze door de sleutel van uw abonnement.

1. Zoek de `SpeechTranslationFromMicrophone_ButtonClicked` teken reeks `YourServiceRegion`in de handler en vervang deze door de [regio](regions.md) die aan uw abonnement is gekoppeld. (Gebruik `westus` bijvoorbeeld voor het gratis proef abonnement.)

1. Kies in de menu balk de optie **bestand** > **Alles opslaan** om uw wijzigingen op te slaan.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

U bent nu klaar om uw toepassing te bouwen en te testen.

1. Selecteer > in de menu balk build**Build Solution** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fout** > **opsporing starten** (of druk op **F5**) om de toepassing te starten. Het venster **HelloWorld** wordt weer gegeven.

   ![Voor beeld van een UWP C# -Vertaal toepassing in-Quick Start](media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Selecteer **microfoon inschakelen**en selecteer wanneer de toegangs machtigings aanvraag wordt weer gegeven, **Ja**.

   ![Verzoek om toegang tot de microfoon](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecteer **spraak vertalen van de microfoon invoer**en spreek een Engelse zin of zin uit in de microfoon van uw apparaat. De toepassing stuurt uw spraak naar de spraak service, waarmee de spraak wordt omgezet in tekst in een andere taal (in dit geval Duits). De speech-service verzendt de vertaalde tekst terug naar de toepassing, waarin de vertaling in het venster wordt weer gegeven.

   ![Gebruikers interface voor spraak omzetting](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Een model trainen voor Custom Speech](how-to-custom-speech-train-model.md)
