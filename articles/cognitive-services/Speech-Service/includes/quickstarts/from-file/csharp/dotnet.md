---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 4db81bcb65077de8cb923117905daebd80532685
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377292"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een resource voor de Azure-spraakservice maken](../../../../overview.md#try-the-speech-service-for-free)
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="open-your-project-in-visual-studio"></a>Open uw project in Visual Studio

De eerste stap is het openen van uw project in Visual Studio.

1. Start Visual Studio 2019.
2. Laad uw project en open `Program.cs`.
3. Download het bestand <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a> en voeg het toe aan uw project.
    - Sla het bestand *whatstheweatherlike.wav* op naast het `Program.cs`-bestand.
    - Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Toevoegen > Bestaand item**.
    - Selecteer het bestand *whatstheweatherlike.wav* en selecteer vervolgens de knop **Toevoegen**.
    - Klik met de rechtermuisknop op het bestand dat u zojuist hebt toegevoegd en selecteer **Eigenschappen**.
    - Wijzig altijd de waarde van **Naar uitvoermap kopiëren** in **Kopiëren indien nieuwer**.

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode

We gaan wat code toevoegen die als basis voor het project gaat dienen. Houd er rekening mee dat u een asynchrone methode met de naam `RecognizeSpeechAsync()` hebt gemaakt.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
        }
    }
}
```

## <a name="create-a-speech-configuration"></a>Een Speech-configuratie maken

Voordat u een `SpeechRecognizer`-object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van de abonnementssleutel en de regio van het abonnement. Voeg deze code toe in de methode `RecognizeSpeechAsync()`.

> [!NOTE]
> In dit voorbeeld wordt de methode `FromSubscription()` gebruikt om de `SpeechConfig` te maken. Zie [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) voor een volledige lijst met beschikbare methoden.
> De Speech-SDK probeert taal standaard te herkennen in en-US. Zie [De brontaal voor spraak-naar-tekst opgeven](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

```csharp
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="create-an-audio-configuration"></a>Een audioconfiguratie maken

Nu moet u een `AudioConfig`-object maken dat verwijst naar het audiobestand. Dit object wordt in een using-instructie gemaakt om de juiste vrijgave van niet-beheerde resources te verzekeren. Voeg deze code toe in de methode `RecognizeSpeechAsync()`, recht onder uw Speech-configuratie.

```csharp
using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
{
}
```

## <a name="initialize-a-speechrecognizer"></a>Een SpeechRecognizer initialiseren

Nu gaan we het `SpeechRecognizer`-object maken met behulp van de `SpeechConfig`- en `AudioConfig`-objecten die u eerder hebt gemaakt. Dit object wordt ook in een using-instructie gemaakt om de juiste vrijgave van niet-beheerde resources te verzekeren. Voer deze code in bij de `RecognizeSpeechAsync()`-methode in de using-instructie van uw ```AudioConfig```-object.

```csharp
using (var recognizer = new SpeechRecognizer(config, audioInput))
{
}
```

## <a name="recognize-a-phrase"></a>Een woordgroep herkennen

Vanuit het `SpeechRecognizer`-object roept u de methode `RecognizeOnceAsync()` aan. Met deze methode laat u de Speech-service weten dat u één woordgroep verstuurt voor herkenning en dat er kan worden gestopt met het herkennen van spraak zodra de woordgroep is geïdentificeerd.

Voeg in de using-instructie deze code toe:

```csharp
Console.WriteLine("Recognizing first result...");
var result = await recognizer.RecognizeOnceAsync();
```

## <a name="display-the-recognition-results-or-errors"></a>De herkenningsresultaten (of fouten) weergeven

Wanneer het herkenningsresultaat wordt geretourneerd door de Speech-service, wilt u daar iets mee doen. We zullen het eenvoudig houden en het resultaat afdrukken naar de console.

In de using-instructie voegt u onder `RecognizeOnceAsync()` deze code toe:

```csharp
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
```

## <a name="check-your-code"></a>Uw code controleren

Op dit punt moet uw code er als volgt uitzien:

```csharp
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
            using (var recognizer = new SpeechRecognizer(config, audioInput))
            {
                Console.WriteLine("Recognizing first result...");
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
}
```

## <a name="build-and-run-your-app"></a>De app bouwen en uitvoeren

U bent nu klaar om uw app te bouwen en de spraakherkenning te testen met behulp van de Speech-service.

1. Compileer de code: Kies in de menubalk van *Visual Studio* **Build** > **Build Solution**.
2. Start uw app: Kies in de menubalk **Debug** > **Start Debugging** of druk op **F5**.
3. Herkenning starten: Uw audiobestand wordt verzonden naar de Speech-service, getranscribeerd als tekst en weergegeven in de console.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
