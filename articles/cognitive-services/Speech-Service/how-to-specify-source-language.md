---
title: Brontaal opgeven voor spraak naar tekst
titleSuffix: Azure Cognitive Services
description: Met de SpraakSDK u de brontaal opgeven wanneer u spraak omzet in tekst. In dit artikel wordt beschreven hoe u de methoden FromConfig en SourceLanguageConfig gebruiken om de spraakservice de brontaal te laten kennen en een aangepast modeldoel te bieden.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235975"
---
# <a name="specify-source-language-for-speech-to-text"></a>Brontaal opgeven voor spraak naar tekst

In dit artikel leert u hoe u de brontaal opgeeft voor een audio-ingang die is doorgegeven aan de Spraak-SDK voor spraakherkenning. Daarnaast wordt voorbeeldcode verstrekt om een aangepast spraakmodel op te geven voor een betere herkenning.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Brontaal opgeven in C #

In dit voorbeeld wordt de brontaal expliciet `SpeechRecognizer` opgegeven als parameter met behulp van construct.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

In dit voorbeeld wordt de `SourceLanguageConfig`brontaal geleverd met behulp van . Vervolgens wordt `sourceLanguageConfig` de doorgegeven `SpeechRecognizer` als een parameter te construeren.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

In dit voorbeeld worden de brontaal en `SourceLanguageConfig`het aangepaste eindpunt geleverd met behulp van . Vervolgens wordt `sourceLanguageConfig` de doorgegeven `SpeechRecognizer` als een parameter te construeren.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`en `EndpointId` ingestelde methoden worden afgeschaft uit `SpeechConfig` de klasse in C#. Het gebruik van deze methoden worden ontmoedigd, en mag niet `SpeechRecognizer`worden gebruikt bij de bouw van een .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Brontaal opgeven in C++

In dit voorbeeld wordt de brontaal expliciet opgegeven `FromConfig` als parameter met behulp van de methode.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

In dit voorbeeld wordt de `SourceLanguageConfig`brontaal geleverd met behulp van . Vervolgens wordt `sourceLanguageConfig` de doorgegeven `FromConfig` als een `recognizer`parameter bij het maken van de .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

In dit voorbeeld worden de brontaal en `SourceLanguageConfig`het aangepaste eindpunt geleverd met behulp van . De `sourceLanguageConfig` wordt doorgegeven `FromConfig` als een `recognizer`parameter bij het maken van de .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`en `SetEndpointId` zijn afgeschafte methoden uit `SpeechConfig` de klasse in C++ en Java. Het gebruik van deze methoden worden ontmoedigd, en mag niet `SpeechRecognizer`worden gebruikt bij de bouw van een .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Brontaal opgeven in Java

In dit voorbeeld wordt de brontaal expliciet `SpeechRecognizer`verstrekt bij het maken van een nieuwe .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

In dit voorbeeld wordt de `SourceLanguageConfig`brontaal geleverd met behulp van . Vervolgens wordt `sourceLanguageConfig` de doorgegeven als een `SpeechRecognizer`parameter bij het maken van een nieuwe .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

In dit voorbeeld worden de brontaal en `SourceLanguageConfig`het aangepaste eindpunt geleverd met behulp van . Vervolgens wordt `sourceLanguageConfig` de doorgegeven als een `SpeechRecognizer`parameter bij het maken van een nieuwe .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`en `setEndpointId` zijn afgeschafte methoden uit `SpeechConfig` de klasse in C++ en Java. Het gebruik van deze methoden worden ontmoedigd, en mag niet `SpeechRecognizer`worden gebruikt bij de bouw van een .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Brontaal opgeven in Python

In dit voorbeeld wordt de brontaal expliciet `SpeechRecognizer` opgegeven als parameter met behulp van construct.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

In dit voorbeeld wordt de `SourceLanguageConfig`brontaal geleverd met behulp van . Vervolgens wordt `SourceLanguageConfig` de doorgegeven `SpeechRecognizer` als een parameter te construeren.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

In dit voorbeeld worden de brontaal en `SourceLanguageConfig`het aangepaste eindpunt geleverd met behulp van . Vervolgens wordt `SourceLanguageConfig` de doorgegeven `SpeechRecognizer` als een parameter te construeren.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`en `endpoint_id` eigenschappen worden afgeschaft uit `SpeechConfig` de klasse in Python. Het gebruik van deze eigenschappen worden ontmoedigd, en mag `SpeechRecognizer`niet worden gebruikt bij de bouw van een .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Brontaal opgeven in Javascript

De eerste stap is `SpeechConfig`het creëren van een:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Geef vervolgens de brontaal van `speechRecognitionLanguage`uw audio op met:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Als u een aangepast model gebruikt voor herkenning, kunt `endpointId`u het eindpunt opgeven met:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Brontaal opgeven in Doelstelling-C

De eerste stap is `speechConfig`het creëren van een:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Geef vervolgens de brontaal van `speechRecognitionLanguage`uw audio op met:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Als u een aangepast model gebruikt voor herkenning, kunt `endpointId`u het eindpunt opgeven met:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Zie ook

* Zie [Taalondersteuning](language-support.md)voor een lijst met ondersteunde talen en landlocaties voor spraak naar tekst.

## <a name="next-steps"></a>Volgende stappen

* [SpraakSDK-referentiedocumentatie](speech-sdk.md)
