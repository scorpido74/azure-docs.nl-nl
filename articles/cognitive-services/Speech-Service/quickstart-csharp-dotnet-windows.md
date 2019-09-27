---
title: 'Quickstart: Speech herkennen, .NET Framework (Windows)-spraak service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om een spraakconsoletoepassing voor -naar-tekst te maken met .NET Framework voor Windows en de Speech-SDK. Als u klaar bent, kunt u de microfoon van de computer gebruiken om in realtime spraak naar tekst te transcriberen.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327053"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Quickstart: Spraak herkennen met de spraak-SDK voor .NET Framework (Windows)

Quick starts zijn ook beschikbaar voor [spraak-synthese](quickstart-text-to-speech-dotnet-windows.md) en [spraak omzetting](quickstart-translate-speech-dotnetframework-windows.md).

Als u wilt, kiest u een andere programmeer taal en/of-omgeving:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Gebruik deze handleiding om een spraakconsoletoepassing voor -naar-tekst te maken met .NET Framework voor Windows en de Speech-SDK. Als u klaar bent, kunt u de microfoon van de computer gebruiken om in realtime spraak naar tekst te transcriberen.

Voor een snelle demonstratie (zonder zelf het Visual Studio-project te maken, zoals beschreven in dit artikel), haalt u de nieuwste [Cognitive Services Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) -voor beelden van github.

## <a name="prerequisites"></a>Vereisten

Als u dit project wilt voltooien, hebt u het volgende nodig:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Een abonnementssleutel voor de Speech Service. [Gratis downloaden](get-started.md).
* Toegang tot de microfoon van uw computer.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open **Program.cs** en vervang de automatisch gegenereerde code door dit voor beeld:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Zoek de teken reeks `YourSubscriptionKey` en vervang deze door uw abonnements sleutel voor spraak Services.

1. Zoek de teken reeks `YourServiceRegion` en vervang deze door de [regio](regions.md) die aan uw abonnement is gekoppeld. Als u bijvoorbeeld het gratis proef abonnement gebruikt, is de regio `westus`.

1. Kies in de menu balk **bestand** > **Alles opslaan**.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

1. Kies in de menu balk **build** > **Build-oplossing** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fouten opsporen** > **fout opsporing starten** (of selecteer **F5**) om de toepassing **HelloWorld** te starten.

1. Spreek een Engelse zin of zin in op de microfoon van uw apparaat. De toepassing stuurt uw spraak naar de spraak Services, waarmee de getranscribeerde tekst terug naar de toepassing wordt verzonden voor weer gave.

   ![Gebruikers interface voor spraak herkenning](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Een model trainen voor Custom Speech](how-to-custom-speech-train-model.md)
