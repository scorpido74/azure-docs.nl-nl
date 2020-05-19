---
title: Concepten van spraak-SDK audio-invoer
titleSuffix: Azure Cognitive Services
description: Een overzicht van de mogelijkheden van de audio-invoer stroom-API van de Speech SDK.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 23a426bf8cc3f30516fff0a672d7118a49666433
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584923"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Over de Speech SDK audio input stream API

De **Audio-invoer stroom** -API van de Speech-SDK biedt een manier om audio naar de recognizers te streamen in plaats van de microfoon of het invoer bestand-api's te gebruiken.

De volgende stappen zijn vereist voor het gebruik van audio-invoer stromen:

- De indeling van de audio stroom identificeren. De indeling moet worden ondersteund door de Speech SDK en de speech-service. Op dit moment wordt alleen de volgende configuratie ondersteund:

  Audio samples in PCM-indeling, één kanaal, 16 bits per sample, 8000 of 16000 samples per seconde (16000 of 32000 bytes per seconde), twee blok align (16 bits inclusief opvulling voor een voor beeld).

  De bijbehorende code in de SDK voor het maken van de audio-indeling ziet er als volgt uit:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000; // or 8000
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Zorg ervoor dat uw code de onbewerkte audio gegevens kan leveren aan de hand van deze specificaties. Als uw audio bron gegevens niet overeenkomen met de ondersteunde indelingen, moet de audio worden omgezet in de vereiste indeling.

- Maak uw eigen audio-invoer stroom klasse die is afgeleid van `PullAudioInputStreamCallback` . Implementeer de `Read()` `Close()` leden en. De exacte functie handtekening is taal afhankelijk, maar de code ziet er ongeveer als volgt uit:

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

- Een audio configuratie maken op basis van de audio-indeling en de invoer stroom. Geef zowel uw normale spraak configuratie als de audio-invoer configuratie op wanneer u uw herkenner maakt. Bijvoorbeeld:

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
- [Zie spraak herkennen in C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
