---
title: Een modus voor spraak herkenning kiezen met de spraak-SDK
titleSuffix: Azure Cognitive Services
description: Meer informatie over het kiezen van de beste herkennings modus wanneer u de Speech SDK gebruikt.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two-with-js
ms.openlocfilehash: ed2c59a92f348812afac3e39719d3814f0720621
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86023037"
---
# <a name="choose-a-speech-recognition-mode"></a>Een modus voor spraak herkenning kiezen

Wanneer u spraak-naar-tekst herkennings bewerkingen overweegt, biedt de [Speech SDK](speech-sdk.md) meerdere modi voor het verwerken van spraak. Conceptueel gezien, ook wel de *herkennings modus*genoemd. In dit artikel worden de verschillende herkennings modi vergeleken.

## <a name="recognize-once"></a>Eenmaal herkennen

Als u elke utterance één zin per keer wilt verwerken, gebruikt u de functie ' eenmaal herkennen '. Met deze methode wordt een herkend utterance gedetecteerd van de invoer vanaf het begin van de gedetecteerde spraak tot de volgende onderbreking. Normaal gesp roken markeert een pauze het einde van een zin of regel.

Aan het einde van een herkende utterance stopt de service de verwerking van de audio van deze aanvraag. De maximum limiet voor herkenning is een lengte van een zin van 20 seconden.

::: zone pivot="programming-language-csharp"

`RecognizeOnceAsync`Zie de [documentatie voor .net Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync)voor meer informatie over het gebruik van de functie.

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

`RecognizeOnceAsync`Zie de [documentatie van C++ Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)voor meer informatie over het gebruik van de functie.

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

`recognizeOnceAsync`Zie de [documentatie voor Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)voor meer informatie over het gebruik van de functie.

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

`recognize_once`Zie de [documentatie van PYTHON Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)voor meer informatie over het gebruik van de functie.

```python
result = speech_recognizer.recognize_once()
```

::: zone-end

::: zone pivot="programming-language-javascript"

`recognizeOnceAsync`Zie de [Speech SDK voor Java script-documenten](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-)voor meer informatie over het gebruik van de functie.

```JavaScript
recognizer.recognizeOnceAsync((result)=>{}, (error)=>{}));
```

::: zone-end

::: zone pivot="programming-language-more"

Zie de [naslag documentatie voor Speech SDK](speech-to-text.md#speech-sdk-reference-docs)voor aanvullende talen.

::: zone-end

## <a name="continuous"></a>Continu

Als u langlopende herkenning nodig hebt, gebruikt u de start-en bijbehorende stop functies voor doorlopende herkenning. De functie start wordt gestart en doorloopt de verwerking van alle uitingen totdat u de functie stop aanroept, of totdat er te veel tijd in stilte is verstreken. Wanneer u de doorlopende modus gebruikt, moet u zich registreren bij de verschillende gebeurtenissen die tijdens het optreden worden gestart. Zo wordt de gebeurtenis ' herkend ' geactiveerd wanneer spraak herkenning wordt uitgevoerd. U moet een gebeurtenis-handler hebben om de herkenning te kunnen afhandelen.

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync(()=>{}, (error)=>{});

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync(()=>{}, (error)=>{});
```

::: zone-end

::: zone pivot="programming-language-more"

Zie de [naslag documentatie voor Speech SDK](speech-to-text.md#speech-sdk-reference-docs)voor aanvullende talen.

::: zone-end

## <a name="dictation"></a>Dicteren

Bij het gebruik van doorlopende herkenning kunt u de verwerking van spraak herkenning inschakelen met behulp van de bijbehorende functie ' dicteren inschakelen '. Deze modus zorgt ervoor dat het spraak configuratie-exemplaar Word-beschrijvingen interpreteert, zoals Lees tekens. Bijvoorbeeld: het utterance ' hebt u Live in het stads vraag teken ' zou worden geïnterpreteerd als de tekst ' wilt u in de stad wonen? '.

::: zone pivot="programming-language-csharp"

`EnableDictation`Zie de [documentatie voor .net Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation)voor meer informatie over het gebruik van de functie.

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

`EnableDictation`Zie de [documentatie van C++ Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)voor meer informatie over het gebruik van de functie.

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

`enableDictation`Zie de [documentatie voor Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)voor meer informatie over het gebruik van de functie.

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

`enable_dictation`Zie de [documentatie van PYTHON Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)voor meer informatie over het gebruik van de functie.

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end

::: zone pivot="programming-language-javascript"

`enableDictation`Zie de [Speech SDK voor Java script-documenten](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--)voor meer informatie over het gebruik van de functie.

```JavaScript
// Enable diction
speechConfig.enableDictation();
```

::: zone-end

::: zone pivot="programming-language-more"

Zie de [naslag documentatie voor Speech SDK](speech-to-text.md#speech-sdk-reference-docs)voor aanvullende talen.

::: zone-end

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanvullende voor beelden van Speech SDK op GitHub verkennen](https://aka.ms/csspeech/samples)
