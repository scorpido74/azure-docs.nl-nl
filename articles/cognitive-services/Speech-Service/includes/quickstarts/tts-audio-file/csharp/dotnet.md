---
title: 'Snelstart: spraak synthetiseren in audiobestand, C# (.NET) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 4ccc68b38d98c332435e252877d258c8591aab8a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925457"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

De eerste stap is ervoor te zorgen dat u uw project open hebt in Visual Studio.

1. Start Visual Studio 2019.
2. Laad uw project `Program.cs`en open .

## <a name="start-with-some-boilerplate-code"></a>Begin met een soort boilerplate-code

Laten we wat code toevoegen die werkt als een skelet voor ons project. Houd er rekening mee dat u `SynthesisToAudioFileAsync()`een async-methode hebt gemaakt met de naam .

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

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Voordat u een `SpeechSynthesizer` object initialiseren, moet u een configuratie maken die uw abonnementssleutel en abonnementsregio gebruikt. Voeg deze code `SynthesisToAudioFileAsync()` in de methode in.

````C#
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
// The default language is "en-us".
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Een audioconfiguratie maken

Nu moet u een ````AudioConfig```` object maken dat naar uw audiobestand verwijst. Dit object wordt gemaakt in een gebruiksinstructie om ervoor te zorgen dat onbeheerde resources correct worden vrijgegeven. Voeg deze code `SynthesisToAudioFileAsync()` in de methode in, direct onder uw spraakconfiguratie.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>Initialiseren een SpeechSynthesizer

Laten we nu het `SpeechSynthesizer` object `SpeechConfig` maken `AudioConfig` met de objecten en objecten die eerder zijn gemaakt. Dit object wordt ook gemaakt in een gebruiksinstructie om ervoor te zorgen dat onbeheerde resources correct worden vrijgegeven. Voeg deze code `SynthesisToAudioFileAsync()` in de methode in ````AudioConfig```` in de instructie met behulp van uw object.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Tekst synthetiseren met SpeakTextAsync

Van `SpeechSynthesizer` het object, ga je `SpeakTextAsync()` de methode aanroepen. Met deze methode wordt uw tekst naar de spraakservice gestuurd die deze omzet in audio. De `SpeechSynthesizer` standaardstem wordt `config.VoiceName` gebruikt als deze niet expliciet is opgegeven.

Voeg deze code toe in de instructie gebruiken:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Controleren op fouten

Wanneer het syntheseresultaat wordt geretourneerd door de spraakservice, moet u controleren of uw tekst is gesynthetiseerd.

Voeg in de `SpeakTextAsync()`instructie met behulp hieronder deze code toe:
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

Op dit punt moet uw code er als volgt uitzien:

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

Nu bent u klaar om uw app te bouwen en onze spraaksynthese te testen met behulp van de Spraakservice.

1. **Compileer de code** - Kies op de menubalk van Visual Studio **Build** > **Solution**.
2. **Start uw app** - Kies op de menubalk Debug**genfout opsporing van** **foutopsporing debuggen** > of druk op **F5**.
3. **Synthese starten** : uw tekst wordt omgezet in spraak en opgeslagen in de opgegeven audiogegevens.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraakvoorbeelden opnemen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
