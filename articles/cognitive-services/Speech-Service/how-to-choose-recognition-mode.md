---
title: Kies een spraakherkenningsmodus met de Spraak-SDK
titleSuffix: Azure Cognitive Services
description: Meer informatie over het kiezen van de beste herkenningsmodus bij het gebruik van de SpraakSDK.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5fdca371e9188ef69068ddbcaa416cbb2b44054c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402155"
---
# <a name="choose-a-speech-recognition-mode"></a>Kies een spraakherkenningsmodus

Wanneer u de spraakherkenningsbewerkingen van spraak naar tekst overweegt, biedt de [Speech SDK](speech-sdk.md) meerdere modi voor het verwerken van spraak. Conceptueel, ook wel de *herkenningsmodus*genoemd. In dit artikel worden de verschillende herkenningsmodi vergeleken.

## <a name="recognize-once"></a>Eén keer herkennen

Als u elke uiting één "zin" tegelijk wilt verwerken, gebruikt u de functie Eén keer herkennen. Deze methode detecteert een herkende uiting van de invoer die begint aan het begin van gedetecteerde spraak tot de volgende pauze. Meestal markeert een pauze het einde van een zin of gedachtegang.

Aan het einde van een herkende utterance stopt de service met het verwerken van audio van dat verzoek. De maximale limiet voor erkenning is een zinsduur van 20 seconden.

::: zone pivot="programming-language-csharp"

Zie de `RecognizeOnceAsync` [.NET Speech SDK-documenten](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync)voor meer informatie over het gebruik van de functie.

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Zie de `RecognizeOnceAsync` [C++ Speech SDK-documenten](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)voor meer informatie over het gebruik van de functie.

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Zie de Java `recognizeOnceAsync` [Speech SDK-documenten](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)voor meer informatie over het gebruik van de functie.

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Zie de Python `recognize_once` [Speech SDK-documenten](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)voor meer informatie over het gebruik van de functie.

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

Zie de [verwijzingsdocumenten voor spraakSDK voor](speech-to-text.md#speech-sdk-reference-docs)extra talen .

::: zone-end

## <a name="continuous"></a>Continu

Als u langdurige herkenning nodig hebt, gebruikt u de start- en bijbehorende stopfuncties voor continue herkenning. De startfunctie start en blijft alle uitingen verwerken totdat u de stopfunctie aanroept, of totdat er te veel tijd in stilte is verstreken. Wanneer u de continue modus gebruikt, moet u zich registreren voor de verschillende gebeurtenissen die zullen vuren bij het optreden. De gebeurtenis 'herkend' wordt bijvoorbeeld geactiveerd wanneer spraakherkenning plaatsvindt. U moet een gebeurtenishandler hebben om met herkenning om te gaan.

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
::: zone pivot="programming-language-more"

Zie de [verwijzingsdocumenten voor spraakSDK voor](speech-to-text.md#speech-sdk-reference-docs)extra talen .

::: zone-end

## <a name="dictation"></a>Dicteren

Bij het gebruik van continue herkenning u dicteerverwerking inschakelen met behulp van de bijbehorende functie 'dicteren inschakelen'. Deze modus zorgt ervoor dat de instantie spraakconfig woordbeschrijvingen van zinsstructuren zoals interpunctie interpreteert. Bijvoorbeeld, de uiting "Woont u in de stad vraagteken" zou worden geïnterpreteerd als de tekst "Woont u in de stad?".

::: zone pivot="programming-language-csharp"

Zie de `EnableDictation` [.NET Speech SDK-documenten](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation)voor meer informatie over het gebruik van de functie.

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Zie de `EnableDictation` [C++ Speech SDK-documenten](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)voor meer informatie over het gebruik van de functie.

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Zie de Java `enableDictation` [Speech SDK-documenten](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)voor meer informatie over het gebruik van de functie.

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Zie de Python `enable_dictation` [Speech SDK-documenten](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)voor meer informatie over het gebruik van de functie.

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

Zie de [verwijzingsdocumenten voor spraakSDK voor](speech-to-text.md#speech-sdk-reference-docs)extra talen .

::: zone-end

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ontdek aanvullende Speech SDK-voorbeelden op GitHub](https://aka.ms/csspeech/samples)
