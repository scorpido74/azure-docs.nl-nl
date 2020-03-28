---
title: 'Snelstart: Spraak-naar-tekst vertalen, C# (UWP) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 12/09/2019
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: e84c7423e0f061004a827ac45c211d7db9c556df
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925609"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

Voeg nu de XAML-code toe die de gebruikersinterface van de toepassing definieert en voeg de C#-code-behind-implementatie toe.

1. Open **in** `MainPage.xaml`Solution Explorer .

1. Plaats in de XAML-weergave van de ontwerper het volgende XAML-fragment in de **rastertag** (tussen `<Grid>` en): `</Grid>`

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. Open **in Solution Explorer**het broncodebestand `MainPage.xaml.cs`met code achter . (Het is gegroepeerd `MainPage.xaml`onder .)

1. Vervang alle code daarin door het volgende fragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. Zoek `SpeechTranslationFromMicrophone_ButtonClicked` in de handler in `YourSubscriptionKey`dit bestand de tekenreeks en vervang deze door uw abonnementssleutel.

1. Zoek `SpeechTranslationFromMicrophone_ButtonClicked` in de handler `YourServiceRegion`de tekenreeks en vervang deze door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. (Gebruik bijvoorbeeld `westus` voor het gratis proefabonnement.)

1. Kies Alles opslaan **op** > de menubalk om**de** wijzigingen op te slaan.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

Nu bent u klaar om uw toepassing te bouwen en te testen.

1. Selecteer op de menubalk **Build** > **Build Solution** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Foutopsporing debuggen** > **debuggen** (of druk op **F5)** om de toepassing te starten. Het **helloworld** venster verschijnt.

   ![Voorbeeld van UWP-vertaaltoepassing in C# - snel starten](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Selecteer **Microfoon inschakelen**en wanneer de aanvraag voor toegangsmachtigingen verschijnt, selecteert u **Ja**.

   ![Machtigingsaanvraag voor toegang voor microfoon](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecteer **Spraak vertalen via de microfooningang**en spreek een Engelse zin of zin in de microfoon van uw apparaat. De applicatie stuurt uw toespraak naar de spraakdienst, die de spraak vertaalt naar tekst in een andere taal (in dit geval Duits). De spraakservice stuurt de vertaalde tekst terug naar de toepassing, waarin de vertaling in het venster wordt weergegeven.

   ![Gebruikersinterface voor spraakvertaling](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
