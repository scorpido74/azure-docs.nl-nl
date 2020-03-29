---
title: Spraak SDK-audio-invoerstreamconcepten
titleSuffix: Azure Cognitive Services
description: Een overzicht van de mogelijkheden van de SpraakSDK's audio input stream API.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 3039276a49e7bb41660d114e78ca047a3f77f279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74109933"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Informatie over de API voor spraakSDK-audioinvoerstream

De **Audio Input Stream** API van de Speech SDK biedt een manier om audio naar de leveranciers te streamen in plaats van de microfoon of de API's voor invoerbestanden te gebruiken.

De volgende stappen zijn vereist bij het gebruik van audio-invoerstromen:

- Identificeer de indeling van de audiostream. Het formaat moet worden ondersteund door de Spraak-SDK en de Spraakservice. Momenteel wordt alleen de volgende configuratie ondersteund:

  Audiosamples in PCM-indeling, één kanaal, 16000 samples per seconde, 32000 bytes per seconde, twee blokuitlijning (16 bits inclusief opvulling voor een monster), 16 bits per monster.

  De bijbehorende code in de SDK om de audio-indeling te maken ziet er als volgt uit:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Zorg ervoor dat uw code de RAW-audiogegevens kan leveren volgens deze specificaties. Als uw audiobrongegevens niet overeenkomen met de ondersteunde indelingen, moet de audio worden getranscodeerd naar de vereiste indeling.

- Maak uw eigen audio-invoerstreamklasse die is afgeleid van `PullAudioInputStreamCallback`. Implementeer `Read()` de `Close()` leden en de leden. De exacte functiehandtekening is taalafhankelijk, maar de code lijkt op dit voorbeeld:

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Maak een audioconfiguratie op basis van uw audio-indeling en invoerstream. Geef zowel uw reguliere spraakconfiguratie als de configuratie van de audio-invoer door wanneer u uw herkenningsapparaat maakt. Bijvoorbeeld:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Volgende stappen

- [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
- [Bekijk hoe u spraak in C herkent #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
