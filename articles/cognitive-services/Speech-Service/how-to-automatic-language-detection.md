---
title: 'Instructies: automatische taal detectie voor spraak naar tekst spraak service gebruiken'
titleSuffix: Azure Cognitive Services
description: De Speech SDK ondersteunt automatische taal detectie voor spraak op tekst. Wanneer u deze functie gebruikt, wordt de opgegeven audio vergeleken met een opgegeven lijst met talen en wordt de meest waarschijnlijke overeenkomst bepaald. De geretourneerde waarde kan vervolgens worden gebruikt om het taal model te selecteren dat wordt gebruikt voor spraak naar tekst.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
ms.openlocfilehash: e156704d3ad54c3437f921fae536a497e1d94868
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506965"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatische taal detectie voor spraak naar tekst

Automatische taal detectie wordt gebruikt om de meest waarschijnlijke overeenkomst te bepalen voor audio die wordt door gegeven aan de Speech SDK in vergelijking met een lijst met de opgegeven talen. De waarde die wordt geretourneerd door automatische taal detectie wordt vervolgens gebruikt voor het selecteren van het taal model voor spraak naar tekst, zodat u een nauw keurigere transcriptie krijgt. Zie [taal ondersteuning](language-support.md)voor meer informatie over de talen die beschikbaar zijn.

In dit artikel leert u hoe u `AutoDetectSourceLanguageConfig` kunt gebruiken om een `SpeechRecognizer` object te maken en de gedetecteerde taal op te halen.

> [!IMPORTANT]
> Deze functie is alleen beschikbaar voor de Speech SDK voor C++ en de Speech SDK voor Java.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatische taal detectie met de Speech SDK

Automatische taal detectie heeft momenteel een limiet aan services zijde van twee talen per detectie. Houd deze beperking in acht wanneer u uw `AudoDetectSourceLanguageConfig`-object wilt bouwen. In de onderstaande voor beelden maakt u een `AutoDetectSourceLanguageConfig`en gebruikt u het voor het maken van een `SpeechRecognizer`.

>[!TIP]
> U kunt ook een aangepast model opgeven dat moet worden gebruikt voor het uitvoeren van spraak op tekst. Zie voor meer informatie [een aangepast model gebruiken voor automatische taal detectie](#use-a-custom-model-for-automatic-language-detection).

De volgende code fragmenten laten zien hoe u automatische taal detectie in uw apps kunt gebruiken:

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

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

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Een aangepast model gebruiken voor automatische taal detectie

Naast taal detectie met behulp van Speech-Service modellen, kunt u een aangepast model voor verbeterde herkenning opgeven. Als er geen aangepast model wordt gegeven, gebruikt de service het standaard taal model.

In de onderstaande fragmenten ziet u hoe u een aangepast model kunt opgeven in de aanroep van de spraak service. Als de gedetecteerde taal `en-US`is, wordt het standaard model gebruikt. Als de gedetecteerde taal `fr-FR`is, wordt het eind punt voor het aangepaste model gebruikt:

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

## <a name="next-steps"></a>Volgende stappen

* [Naslag documentatie voor Speech SDK](speech-sdk.md)
