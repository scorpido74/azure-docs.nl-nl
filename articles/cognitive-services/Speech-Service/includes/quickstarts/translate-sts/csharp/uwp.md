---
title: 'Snelstartgids: vertalen van speech-to-speech, C# (UWP)-Speech Service'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: 08001aabd279b2df64635719e9ef3e0772df62e9
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80671143"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Stel uw ontwikkel omgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

Voeg nu de XAML-code toe die de gebruikers interface van de toepassing definieert en voeg de C#-code-behind-implementatie toe.

1. Open **Solution Explorer** `MainPage.xaml`in Solution Explorer.

1. In de XAML-weer gave van de ontwerp functie plaatst u het volgende XAML-fragment in `<Grid>` de `</Grid>` **grid** -tag (tussen en):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. Open in **Solution Explorer**het bron bestand `MainPage.xaml.cs`voor de code achter. (Deze wordt gegroepeerd onder `MainPage.xaml`.)

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. Zoek in `SpeechTranslationFromMicrophone_ButtonClicked` de handler in dit bestand naar de teken `YourSubscriptionKey`reeks en vervang deze door de sleutel van uw abonnement.

1. Zoek de `SpeechTranslationFromMicrophone_ButtonClicked` teken reeks `YourServiceRegion`in de handler en vervang deze door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. (Gebruik `westus` bijvoorbeeld voor het gratis proef abonnement.)

1. Kies in de menu balk de optie **bestand** > **Alles opslaan** om uw wijzigingen op te slaan.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

U bent nu klaar om uw toepassing te bouwen en te testen.

1. **Selecteer** > in de menu balk build**Build Solution** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fout** > **opsporing starten** (of druk op **F5**) om de toepassing te starten. Het venster **HelloWorld** wordt weer gegeven.

   ![Voor beeld van een UWP-Vertaal toepassing in C#-Quick Start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Selecteer **microfoon inschakelen**en selecteer wanneer de toegangs machtigings aanvraag wordt weer gegeven, **Ja**.

   ![Verzoek om toegang tot de microfoon](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecteer **spraak vertalen van de microfoon invoer**en spreek een Engelse zin of zin uit in de microfoon van uw apparaat. De toepassing stuurt uw spraak naar de spraak service, waarmee de spraak wordt omgezet in tekst in een andere taal (in dit geval Duits). De speech-service verzendt de vertaalde tekst terug naar de toepassing, waarin de vertaling in het venster wordt weer gegeven.

   ![Gebruikers interface voor spraak omzetting](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
