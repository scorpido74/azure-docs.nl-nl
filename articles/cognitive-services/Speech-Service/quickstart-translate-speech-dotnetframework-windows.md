---
title: 'Quickstart: Vertaal spraak, C# (.NET Framework Windows): spraak service'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start maakt u een .NET Framework-toepassing voor het vastleggen van gebruikers spraak, het vertalen ervan naar een andere taal en het uitvoeren van de tekst naar de opdracht regel. Deze handleiding is bedoeld voor Windows-gebruikers.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327345"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>Quickstart: Spraak omzetten met de Speech SDK voor .NET Framework (Windows)

Quick starts zijn ook beschikbaar voor [spraak herkenning](quickstart-csharp-dotnet-windows.md) en [spraak synthese](quickstart-text-to-speech-dotnet-windows.md).

In deze Quick Start maakt u een .NET Framework-toepassing die de gebruikers spraak van de microfoon van uw computer vastlegt, de spraak vertaalt en de omgezette tekst in realtime overschakelt naar de opdracht regel. Deze toepassing kan worden uitgevoerd op 32-bits of 64-bits Windows en is gebouwd met het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget) en micro soft Visual Studio 2019.

Zie [Taalondersteuning](language-support.md) voor een volledige lijst met talen die voor spraakomzetting beschikbaar zijn.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Een Azure-abonnementssleutel voor de Spraakservice. [Gratis downloaden](get-started.md).

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open **Program.cs**en vervang alle code hierin door het volgende.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Zoek de teken reeks `YourSubscriptionKey` en vervang deze door de sleutel van uw abonnement.

1. Zoek de teken reeks `YourServiceRegion` en vervang deze door de [regio](regions.md) die aan uw abonnement is gekoppeld. Als u bijvoorbeeld het gratis proef abonnement gebruikt, is de regio `westus`.

1. Kies in de menu balk **bestand** > **Alles opslaan**.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Kies in de menu balk **build** > **Build-oplossing** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fouten opsporen** > **fout opsporing starten** (of selecteer **F5**) om de toepassing **HelloWorld** te starten.

1. Spreek een Engelse zin of zin in op de microfoon van uw apparaat. De toepassing stuurt uw spraak naar de spraak service, waarmee de spraak wordt omgezet in tekst in een andere taal (in dit geval Duits). De speech-service verzendt de vertaalde tekst terug naar de toepassing, waarin de vertaling in het venster wordt weer gegeven.

   ![Gebruikers interface voor spraak omzetting](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>Volgende stappen

Op GitHub vindt u aanvullende voorbeelden, zoals hoe u spraak kunt lezen vanuit een audiobestand en omgezette tekst als kunstmatige spraak kunt uitvoeren.

> [!div class="nextstepaction"]
> [C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Een model trainen voor Custom Speech](how-to-custom-speech-train-model.md)
