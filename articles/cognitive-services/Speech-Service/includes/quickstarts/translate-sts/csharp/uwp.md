---
title: 'Snelstartgids: vertalen van speech-to-speech C# , (UWP)-Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 12/09/2019
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: 3fa79a34f5ca588b943512a6ba565261a3e2c6ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "76156672"
---
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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. Open in **Solution Explorer**het bron bestand van de code-behind `MainPage.xaml.cs`. (Deze is gegroepeerd onder `MainPage.xaml`.)

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. Zoek in de handler `SpeechTranslationFromMicrophone_ButtonClicked` in dit bestand de teken reeks `YourSubscriptionKey`en vervang deze door de sleutel van uw abonnement.

1. Zoek in de handler `SpeechTranslationFromMicrophone_ButtonClicked` de teken reeks `YourServiceRegion`en vervang deze door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. (Gebruik `westus` voor het gratis proef abonnement.)

1. Kies in de menu balk de optie **bestand** > **Alles opslaan** om uw wijzigingen op te slaan.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

U bent nu klaar om uw toepassing te bouwen en te testen.

1. Selecteer in de menu balk de optie **build** > **Build** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fout opsporing** > **fout opsporing starten** (of druk op **F5**) om de toepassing te starten. Het venster **HelloWorld** wordt weer gegeven.

   ![Voor beeld van een UWP C# -Vertaal toepassing in-Quick Start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Selecteer **microfoon inschakelen**en selecteer wanneer de toegangs machtigings aanvraag wordt weer gegeven, **Ja**.

   ![Verzoek om toegang tot de microfoon](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecteer **spraak vertalen van de microfoon invoer**en spreek een Engelse zin of zin uit in de microfoon van uw apparaat. De toepassing stuurt uw spraak naar de spraak service, waarmee de spraak wordt omgezet in tekst in een andere taal (in dit geval Duits). De speech-service verzendt de vertaalde tekst terug naar de toepassing, waarin de vertaling in het venster wordt weer gegeven.

   ![Gebruikers interface voor spraak omzetting](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
