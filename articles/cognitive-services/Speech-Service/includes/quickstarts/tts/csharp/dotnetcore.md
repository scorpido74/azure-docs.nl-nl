---
title: 'Quick Start: een functie voor C# het bemonsteren van spraak, (.net core)-spraak service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het met behulp C# van de spraak-SDK voor het maken van spraak in .net core in Windows
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 670d32a93df9e2a0363163079b50c7306f81975e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502910"
---
> [!NOTE]
> .NET Core is een open-source, platformoverschrijdend .NET-platform waarmee de [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard)-specificatie wordt geïmplementeerd.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?tabs=dotnetcore)
## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open `Program.cs` en vervang daarin alle code door het volgende.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang ook de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die gekoppeld is aan uw abonnement (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Sla de wijzigingen in het project op.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

1. Compileer de toepassing. Kies in de menubalk **Build** > **Build Solution**. De code moet zonder fouten worden gecompileerd.

    ![Scherm opname van de Visual Studio-toepassing, met de optie build Solution gemarkeerd](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Geslaagde build")

1. Start de toepassing. Selecteer in de menubalk **Fouten opsporen** > **Foutopsporing starten** of druk op **F5**.

    ![Scherm opname van Visual Studio-toepassing, met de optie fout opsporing starten gemarkeerd](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "De app starten in fout opsporing")

1. Er wordt een console venster weer gegeven waarin u wordt gevraagd om tekst te typen. Typ enkele woorden of een zin. De tekst die u hebt getypt, wordt verzonden naar de spraak Services en gesynthesizerd op spraak, die op uw spreker wordt afgespeeld.

    ![Scherm opname van de console-uitvoer na een geslaagde synthese](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Console-uitvoer na geslaagde synthese")

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraak voorbeelden vastleggen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
