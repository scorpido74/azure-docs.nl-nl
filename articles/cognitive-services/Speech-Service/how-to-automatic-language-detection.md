---
title: Automatische taaldetectie gebruiken voor spraak naar tekst
titleSuffix: Azure Cognitive Services
description: De Speech SDK ondersteunt automatische taaldetectie voor spraak naar tekst. Bij het gebruik van deze functie wordt de meegeleverde audio vergeleken met een opgegeven lijst met talen en wordt de meest waarschijnlijke overeenkomst bepaald. De geretourneerde waarde kan vervolgens worden gebruikt om het taalmodel te selecteren dat wordt gebruikt voor spraak naar tekst.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5592fc3e50db892c6abb09fc2516b8e1c03f0f03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239605"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatische taaldetectie voor spraak naar tekst

Automatische taaldetectie wordt gebruikt om te bepalen welke waarschijnlijk e-match voor audio doorgegeven aan de Speech SDK in vergelijking met een lijst van de verstrekte talen. De waarde die wordt geretourneerd door automatische taaldetectie wordt vervolgens gebruikt om het taalmodel voor spraak naar tekst te selecteren, zodat u een nauwkeurigere transcriptie krijgt. Zie [Taalondersteuning](language-support.md)om te zien welke talen beschikbaar zijn.

In dit artikel leert u hoe `AutoDetectSourceLanguageConfig` u `SpeechRecognizer` een object maken en de gedetecteerde taal ophalen.

> [!IMPORTANT]
> Deze functie is alleen beschikbaar voor de Speech SDK voor C#, C++, Java en Python.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatische taaldetectie met de SpraakSDK

Automatische taaldetectie heeft momenteel een service-side limiet van twee talen per detectie. Houd deze beperking in `AudoDetectSourceLanguageConfig` gedachten bij het bouwen van uw object. In de onderstaande voorbeelden maakt `AutoDetectSourceLanguageConfig`u een , `SpeechRecognizer`vervolgens gebruiken om een te construeren .

> [!TIP]
> U ook een aangepast model opgeven dat u wilt gebruiken bij het uitvoeren van spraak naar tekst. Zie [Een aangepast model gebruiken voor automatische taaldetectie voor](#use-a-custom-model-for-automatic-language-detection)meer informatie.

In de volgende fragmenten wordt uitgelegd hoe u automatische taaldetectie in uw apps gebruiken:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Een aangepast model gebruiken voor automatische taaldetectie

Naast taaldetectie met spraakservicemodellen, u een aangepast model opgeven voor verbeterde herkenning. Als er geen aangepast model is geleverd, gebruikt de service het standaardtaalmodel.

In de onderstaande fragmenten u een aangepast model opgeven in uw gesprek met de spraakservice. Als de gedetecteerde `en-US`taal is, wordt het standaardmodel gebruikt. Als de gedetecteerde `fr-FR`taal is, wordt het eindpunt voor het aangepaste model gebruikt:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

## <a name="next-steps"></a>Volgende stappen

- [SpraakSDK-referentiedocumentatie](speech-sdk.md)
