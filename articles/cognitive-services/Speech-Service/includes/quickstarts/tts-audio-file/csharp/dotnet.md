---
title: 'Quick Start: spraak samen te brengen in audio bestand, C# (.NET)-Speech Service'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: erhopf
ms.openlocfilehash: d3d0a807e8742419ee321f38daebf6f70d6b0be1
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275078"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Stel uw ontwikkel omgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

De eerste stap is om ervoor te zorgen dat uw project in Visual Studio is geopend.

1. Start Visual Studio 2019.
2. Laad uw project en open `Program.cs`het.

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt. Houd er rekening mee dat u een asynchrone methode hebt `SynthesisToAudioFileAsync()`gemaakt met de naam.

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Voordat u een `SpeechSynthesizer` object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van de abonnements sleutel en de regio van het abonnement. Voeg deze code in de `SynthesisToAudioFileAsync()` methode in.

````C#
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
// The default language is "en-us".
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Een audio configuratie maken

Nu moet u een ````AudioConfig```` object maken dat verwijst naar uw audio bestand. Dit object wordt gemaakt in een using-instructie om ervoor te zorgen dat onbeheerde bronnen goed worden vrijgegeven. Voeg deze code toe in `SynthesisToAudioFileAsync()` de-methode, rechts onder uw spraak configuratie.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>Een SpeechSynthesizer initialiseren

Nu gaan we het `SpeechSynthesizer` object maken met behulp `SpeechConfig` van `AudioConfig` de-en-objecten die u eerder hebt gemaakt. Dit object wordt ook gemaakt in een using-instructie om te zorgen voor de juiste release van onbeheerde resources. Voeg deze code toe in `SynthesisToAudioFileAsync()` de-methode in de instructie using waarmee uw ````AudioConfig```` object wordt ingepakt.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Tekst syntheseen met SpeakTextAsync

Vanuit het `SpeechSynthesizer` object roept u de `SpeakTextAsync()` -methode aan. Met deze methode wordt uw tekst naar de speech-service verzonden waarmee deze naar audio wordt geconverteerd. De `SpeechSynthesizer` maakt gebruik van de standaard stem `config.VoiceName` als deze niet expliciet is opgegeven.

Voeg in de instructie using deze code toe:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Controleren op fouten

Wanneer het resultaat van de synthese wordt geretourneerd door de spraak service, moet u controleren of uw tekst correct is gesynthesizerd.

Voeg de volgende `SpeakTextAsync()`code toe binnen de instructie using:
````C#
if (result.Reason == ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>Controleer uw code

Op dit moment moet uw code er als volgt uitzien:

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            var fileName = "helloworld.wav";
            using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
            {
                using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
                {
                    var text = "Hello world!";
                    var result = await synthesizer.SpeakTextAsync(text);

                    if (result.Reason == ResultReason.SynthesizingAudioCompleted)
                    {
                        Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

U bent nu klaar om uw app te bouwen en onze spraak synthese te testen met behulp van de speech-service.

1. **De code compileren** **: Kies** > build**Build Solution**in de menu balk van Visual Studio.
2. **Start uw app** -vanuit de menu balk, kies **fout** > **opsporing starten** of druk op **F5**.
3. **Synthese starten** : uw tekst wordt geconverteerd naar spraak en opgeslagen in de opgegeven audio gegevens.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraak voorbeelden vastleggen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
