---
title: 'Snelstart: Spraak synthetiseren, C# (.NET Core) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het synthetiseren van spraak in C# onder .NET Core op Windows met behulp van de SpraakSDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: ba6a373f678f8d34b34a669b3fbb3b570c90ed0d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78926117"
---
> [!NOTE]
> .NET Core is een open-source, platformoverschrijdend .NET-platform waarmee de [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard)-specificatie wordt geïmplementeerd.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open `Program.cs` en vervang daarin alle code door het volgende.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang ook de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Sla de wijzigingen in het project op.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

1. Compileer de toepassing. Kies > **Build-oplossing** **Build**in de menubalk. De code moet zonder fouten worden gecompileerd.

    ![Schermafbeelding van Visual Studio-toepassing met de optie Build Solution gemarkeerd](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Succesvol bouwen")

1. Start de toepassing. Kies foutopsporing >  **debuggen debuggen**of druk op **F5**in de menubalk.**Start Debugging**

    ![Schermafbeelding van Visual Studio-toepassing met de optie Start Debugging gemarkeerd](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "De app starten met foutopsporing")

1. Er verschijnt een consolevenster waarin u wordt gevraagd tekst te typen. Typ een paar woorden of een zin. De tekst die u hebt getypt, wordt naar de spraakservice verzonden en gesynthetiseerd naar spraak, die op uw luidspreker wordt afgespeeld.

    ![Schermafbeelding van console-uitvoer na succesvolle synthese](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Console-uitvoer na succesvolle synthese")

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraakvoorbeelden opnemen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
