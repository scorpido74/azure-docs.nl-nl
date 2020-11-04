---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: 040ffea69f76255dcb1bfc6787cad45a95baa904
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305890"
---
Laad eerst uw trefwoorden model bestand met behulp van de `FromFile()` statische functie. dit retourneert een `KeywordRecognitionModel` . Gebruik het pad naar het `.table` bestand dat u hebt gedownload vanuit speech Studio. Daarnaast maakt u een `AudioConfig` met de standaard microfoon en vervolgens een nieuw exemplaar `KeywordRecognizer` van de audio configuratie.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Vervolgens wordt het uitvoeren van trefwoord herkenning uitgevoerd met één aanroep naar `RecognizeOnceAsync()` door uw model object door te geven. Hiermee wordt een sessie voor het herkennen van een tref woord gestart totdat het sleutel woord is herkend. Daarom gebruikt u dit ontwerp patroon doorgaans in toepassingen met meerdere threads of in gevallen waarin u voor onbepaalde tijd kunt wachten op een Wake-woord.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> In het voor beeld dat hier wordt weer gegeven, wordt gebruikgemaakt van lokale woord herkenning, omdat hiervoor geen `SpeechConfig` object voor verificatie context is vereist en er geen contact wordt gemaakt met de back-end. U kunt echter zowel trefwoord herkenning als verificatie uitvoeren waarbij [een continue back-endverbinding wordt gebruikt](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword).