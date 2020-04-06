---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 4e960a2dee87db77647ba1596a9c22138c64d15c
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671762"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een Azure-spraakbron maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)
> * Zorg ervoor dat u toegang hebt tot een microfoon voor audio-opname

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

De eerste stap is ervoor te zorgen dat u uw project open hebt in Visual Studio.

1. Start **Visual Studio 2019**.
2. Laad uw project en open *Program.cs.*

## <a name="source-code"></a>Broncode

Vervang de inhoud van het *Program.cs* bestand door de volgende C#-code.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace Speech.Recognition
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();

            Console.WriteLine("Please press any key to continue...");
            Console.ReadLine();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config =
                SpeechConfig.FromSubscription(
                    "YourSubscriptionKey",
                    "YourServiceRegion");

            using var recognizer = new SpeechRecognizer(config);
            
            var result = await recognizer.RecognizeOnceAsync();
            switch (result.Reason)
            {
                case ResultReason.RecognizedSpeech:
                    Console.WriteLine($"We recognized: {result.Text}");
                    break;
                case ResultReason.NoMatch:
                    Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    break;
                case ResultReason.Canceled:
                    var cancellation = CancellationDetails.FromResult(result);
                    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
    
                    if (cancellation.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                    }
                    break;
            }
        }
    }
}
```

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Code uitleg

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>App bouwen en uitvoeren

Nu bent u klaar om uw app opnieuw op te bouwen en de spraakherkenningsfunctionaliteit te testen met behulp van de spraakservice.

1. **Compileer de code** - Kies op de menubalk van Visual Studio **Build** > **Solution**.
2. **Start uw app** - Kies op de menubalk Debug**genfout opsporing van** **foutopsporing debuggen** > of druk op <kbd>F5</kbd>.
3. **Start erkenning** - Het zal u vragen om een zin in het Engels te spreken. Uw toespraak wordt verzonden naar de spraakservice, getranscribeerd als tekst en weergegeven in de console.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](../footer.md)]
