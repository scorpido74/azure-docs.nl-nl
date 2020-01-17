---
title: Automatische taal detectie voor spraak naar tekst gebruiken
titleSuffix: Azure Cognitive Services
description: De Speech SDK ondersteunt automatische taal detectie voor spraak op tekst. Wanneer u deze functie gebruikt, wordt de opgegeven audio vergeleken met een opgegeven lijst met talen en wordt de meest waarschijnlijke overeenkomst bepaald. De geretourneerde waarde kan vervolgens worden gebruikt om het taal model te selecteren dat wordt gebruikt voor spraak naar tekst.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-seven
ms.openlocfilehash: bc438c3e606fefc10e9ffbb64c79f7167d9af062
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122046"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatische taal detectie voor spraak naar tekst

Automatische taal detectie wordt gebruikt om de meest waarschijnlijke overeenkomst te bepalen voor audio die wordt door gegeven aan de Speech SDK in vergelijking met een lijst met de opgegeven talen. De waarde die wordt geretourneerd door automatische taal detectie wordt vervolgens gebruikt voor het selecteren van het taal model voor spraak naar tekst, zodat u een nauw keurigere transcriptie krijgt. Zie [taal ondersteuning](language-support.md)voor meer informatie over de talen die beschikbaar zijn.

In dit artikel leert u hoe u `AutoDetectSourceLanguageConfig` kunt gebruiken om een `SpeechRecognizer` object te maken en de gedetecteerde taal op te halen.

> [!IMPORTANT]
> Deze functie is alleen beschikbaar voor de speech- C# C++ SDK voor en Java.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatische taal detectie met de Speech SDK

Automatische taal detectie heeft momenteel een limiet aan services zijde van twee talen per detectie. Houd deze beperking in acht wanneer u uw `AudoDetectSourceLanguageConfig`-object wilt bouwen. In de onderstaande voor beelden maakt u een `AutoDetectSourceLanguageConfig`en gebruikt u het voor het maken van een `SpeechRecognizer`.

> [!TIP]
> U kunt ook een aangepast model opgeven dat moet worden gebruikt voor het uitvoeren van spraak op tekst. Zie voor meer informatie [een aangepast model gebruiken voor automatische taal detectie](#use-a-custom-model-for-automatic-language-detection).

De volgende code fragmenten laten zien hoe u automatische taal detectie in uw apps kunt gebruiken:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "de-DE" });
using (var recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Een aangepast model gebruiken voor automatische taal detectie

Naast taal detectie met behulp van Speech-Service modellen, kunt u een aangepast model voor verbeterde herkenning opgeven. Als er geen aangepast model wordt gegeven, gebruikt de service het standaard taal model.

In de onderstaande fragmenten ziet u hoe u een aangepast model kunt opgeven in de aanroep van de spraak service. Als de gedetecteerde taal `en-US`is, wordt het standaard model gebruikt. Als de gedetecteerde taal `fr-FR`is, wordt het eind punt voor het aangepaste model gebruikt:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

## <a name="next-steps"></a>Volgende stappen

- [Naslag documentatie voor Speech SDK](speech-sdk.md)
