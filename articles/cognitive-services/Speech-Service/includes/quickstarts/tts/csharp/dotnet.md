---
title: 'Snelstart: spraak synthetiseren, C# (Windows) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om een tekst-naar-spraakconsoletoepassing te maken met behulp van het .NET-framework voor Windows en de SpraakSDK. Wanneer u klaar bent, u spraak uit tekst synthetiseren en de spraak op uw spreker in realtime horen.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: b615ba8085650e9aa686fb4a229d9752c4f6e2ce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925124"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=dotnet)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open **Program.cs** en vervang de automatisch gegenereerde code door dit voorbeeld:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Zoek de `YourSubscriptionKey`tekenreeks en vervang deze door uw abonnementssleutel voor spraakservice.

1. Zoek de `YourServiceRegion`tekenreeks en vervang deze door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. Als u bijvoorbeeld het gratis proefabonnement gebruikt, is `westus`de regio .

1. Kies Alles**opslaan** **op** > de menubalk .

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Kies**build-oplossing** **bouwen** > om de toepassing te bouwen op de menubalk. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Foutopsporing debuggen** > **(of** selecteer **F5)** om de **helloworld-toepassing** te starten.

1. Voer een Engelse zin of zin in. De toepassing verzendt uw tekst naar de spraakservice, die gesynthetiseerde spraak naar de toepassing verzendt om op uw luidspreker af te spelen.

   ![Gebruikersinterface voor spraaksynthese](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraakvoorbeelden opnemen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
