---
title: 'Quickstart: Spraak-naar-spraak vertalen, C# (UWP) - Speech-service'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.author: jhakulin
ms.topic: include
ms.openlocfilehash: 2af0d0eb2411c5ffe9f80498a3e93d48fe0a769b
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057791"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een resource voor de Azure Speech-service maken](../../../../get-started.md)
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

Voeg nu de XAML-code toe die de gebruikersinterface van de toepassing definieert en voeg de implementatie van C#-code-behind toe.

1. In **Solution Explorer** opent u `MainPage.xaml`.

1. Voeg in de XAML-weergave van de ontwerpfunctie het volgende XAML-fragment toe aan de tag **Grid** (tussen `<Grid>` en `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. Open in **Solution Explorer** het code-behind-bronbestand `MainPage.xaml.cs`. (Het is gegroepeerd onder `MainPage.xaml`.)

1. Vervang alle code in het bestand door het volgende codefragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. Zoek de tekenreeks `YourSubscriptionKey` in de handler `SpeechTranslationFromMicrophone_ButtonClicked` in dit bestand en vervang deze door uw abonnementssleutel.

1. Zoek de tekenreeks `YourServiceRegion` in de handler `SpeechTranslationFromMicrophone_ButtonClicked` en vervang deze door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. (Gebruik bijvoorbeeld `westus` voor het gratis proefabonnement.)

1. Kies in de menubalk **File** > **Save All** om de wijzigingen op te slaan.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

U bent nu klaar om uw toepassing te bouwen en testen.

1. Selecteer in de menubalk **Build** > **Build Solution** om de toepassing te compileren. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Debug** > **Start Debugging** (of druk op **F5**) om de toepassing te starten. Het venster **helloworld** wordt weergegeven.

   ![Voorbeeld van UWP-vertaaltoepassing in C# - quickstart](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Selecteer **Enable Microphone**, en selecteer **Yes** wanneer het toegangsverzoek verschijnt.

   ![Verzoek om toegang tot microfoon](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecteer **Translate speech from the microphone input** en zeg een Engelse woordgroep of zin in de microfoon van uw apparaat. De toepassing stuurt de gesproken tekst naar de Speech-service, waarna de tekst wordt omgezet in tekst in een andere taal (in dit geval Duits). De Speech-service verzendt de vertaalde tekst terug naar de toepassing, waarna de vertaling in het venster van de toepassing wordt weergegeven.

   ![Gebruikersinterface voor spraakvertaling](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
