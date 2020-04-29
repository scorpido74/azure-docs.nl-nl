---
title: De bron taal voor spraak naar tekst opgeven
titleSuffix: Azure Cognitive Services
description: Met de Speech SDK kunt u de bron taal opgeven bij het converteren van spraak naar tekst. In dit artikel wordt beschreven hoe u de FromConfig-en SourceLanguageConfig-methoden gebruikt om de spraak service de bron taal te laten kennen en een aangepast model doel te bieden.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f0723534d9d2187593cb73f058ffea62473b80a9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80235975"
---
# <a name="specify-source-language-for-speech-to-text"></a>De bron taal voor spraak naar tekst opgeven

In dit artikel leert u hoe u de bron taal kunt opgeven voor een audio-invoer die wordt door gegeven aan de Speech SDK voor spraak herkenning. Daarnaast wordt er een voorbeeld code gegeven om een aangepast spraak model op te geven voor verbeterde herkenning.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>De bron taal in C opgeven #

In dit voor beeld wordt de bron taal expliciet als een para meter met `SpeechRecognizer` construct gegeven.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

In dit voor beeld wordt de bron taal met behulp van gebruikt `SourceLanguageConfig`. Vervolgens wordt de `sourceLanguageConfig` door gegeven als een para meter `SpeechRecognizer` die moet worden samengesteld.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

In dit voor beeld worden de bron taal en het aangepaste eind punt `SourceLanguageConfig`met behulp van gebruikt. Vervolgens wordt de `sourceLanguageConfig` door gegeven als een para meter `SpeechRecognizer` die moet worden samengesteld.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`en `EndpointId` set-methoden zijn afgeschaft `SpeechConfig` van de klasse in C#. Het gebruik van deze methoden wordt afgeraden en mag niet worden gebruikt bij het maken van een `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>De bron taal opgeven in C++

In dit voor beeld wordt de bron taal expliciet als een para meter voor de `FromConfig` methode gebruikt.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

In dit voor beeld wordt de bron taal met behulp van gebruikt `SourceLanguageConfig`. Vervolgens wordt de `sourceLanguageConfig` door gegeven als para meter bij `FromConfig` het maken van `recognizer`de.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

In dit voor beeld worden de bron taal en het aangepaste eind punt `SourceLanguageConfig`met behulp van gebruikt. De `sourceLanguageConfig` wordt door gegeven als para meter `FromConfig` bij het maken `recognizer`van de.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`en `SetEndpointId` zijn afgeschafte methoden `SpeechConfig` van de klasse in C++ en Java. Het gebruik van deze methoden wordt afgeraden en mag niet worden gebruikt bij het maken van een `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Een bron taal opgeven in Java

In dit voor beeld wordt de bron taal expliciet bij het maken van een `SpeechRecognizer`nieuw gegeven.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

In dit voor beeld wordt de bron taal met behulp van gebruikt `SourceLanguageConfig`. Vervolgens wordt de `sourceLanguageConfig` door gegeven als para meter bij het maken van `SpeechRecognizer`een nieuw.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

In dit voor beeld worden de bron taal en het aangepaste eind punt `SourceLanguageConfig`met behulp van gebruikt. Vervolgens wordt de `sourceLanguageConfig` door gegeven als para meter bij het maken van `SpeechRecognizer`een nieuw.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`en `setEndpointId` zijn afgeschafte methoden `SpeechConfig` van de klasse in C++ en Java. Het gebruik van deze methoden wordt afgeraden en mag niet worden gebruikt bij het maken van een `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>De bron taal in python opgeven

In dit voor beeld wordt de bron taal expliciet als een para meter met `SpeechRecognizer` construct gegeven.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

In dit voor beeld wordt de bron taal met behulp van gebruikt `SourceLanguageConfig`. Vervolgens wordt de `SourceLanguageConfig` door gegeven als een para meter `SpeechRecognizer` die moet worden samengesteld.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

In dit voor beeld worden de bron taal en het aangepaste eind punt `SourceLanguageConfig`met behulp van gebruikt. Vervolgens wordt de `SourceLanguageConfig` door gegeven als een para meter `SpeechRecognizer` die moet worden samengesteld.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`en `endpoint_id` eigenschappen zijn afgeschaft van `SpeechConfig` de klasse in python. Het gebruik van deze eigenschappen wordt afgeraden en mag niet worden gebruikt bij het maken van een `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Een bron taal opgeven in Java script

De eerste stap is het maken van `SpeechConfig`een:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Geef vervolgens de bron taal van uw audio op met `speechRecognitionLanguage`:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Als u een aangepast model gebruikt voor herkenning, kunt u het eind punt opgeven met `endpointId`:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>De bron taal opgeven in de doel stelling-C

De eerste stap is het maken van `speechConfig`een:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Geef vervolgens de bron taal van uw audio op met `speechRecognitionLanguage`:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Als u een aangepast model gebruikt voor herkenning, kunt u het eind punt opgeven met `endpointId`:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Zie ook

* Zie [taal ondersteuning](language-support.md)voor een lijst met ondersteunde talen en land instellingen voor spraak naar tekst.

## <a name="next-steps"></a>Volgende stappen

* [Naslag documentatie voor Speech SDK](speech-sdk.md)
