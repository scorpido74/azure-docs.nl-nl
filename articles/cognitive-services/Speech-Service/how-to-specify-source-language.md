---
title: 'Procedure: de bron taal voor spraak naar tekst spraak service opgeven'
titleSuffix: Azure Cognitive Services
description: Met de Speech SDK kunt u de bron taal opgeven bij het converteren van spraak naar tekst. In dit artikel wordt beschreven hoe u de FromConfig-en SourceLanguageConfig-methoden gebruikt om de spraak service de bron taal te laten kennen en een aangepast model doel te bieden.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 3c2503d2f341b4cdf90f7f7690fed897412a9614
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506899"
---
# <a name="specify-source-language-for-speech-to-text"></a>De bron taal voor spraak naar tekst opgeven

In dit artikel leert u hoe u de bron taal kunt opgeven voor een audio-invoer die wordt door gegeven aan de Speech SDK voor spraak herkenning. Daarnaast wordt er een voorbeeld code gegeven om een aangepast spraak model op te geven voor verbeterde herkenning.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>De bron taal opgeven inC#

De eerste stap bestaat uit het maken van een `SpeechConfig`:

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Geef vervolgens de bron taal van uw audio op met `SpeechRecognitionLanguage`:

```csharp
speechConfig.SpeechRecognitionLanguage = "de-DE";
```

Als u een aangepast model gebruikt voor herkenning, kunt u het eind punt opgeven met `EndpointId`:

```csharp
speechConfig.EndpointId = "The Endpoint ID for your custom model.";
```

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>De bron taal opgeven inC++

In dit voor beeld wordt de bron taal expliciet als een para meter gegeven met behulp van de `FromConfig` methode.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

In dit voor beeld wordt de bron taal gegeven met behulp van `SourceLanguageConfig`. Vervolgens wordt de `sourceLanguageConfig` door gegeven als een para meter voor `FromConfig` bij het maken van de `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

In dit voor beeld worden de bron taal en het aangepaste eind punt voorzien van `SourceLanguageConfig`. De `sourceLanguageConfig` wordt door gegeven als een para meter voor `FromConfig` bij het maken van de `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` en `SetEndpointId` zijn afgeschafte methoden van de `SpeechConfig` C++ -klasse in en Java. Het gebruik van deze methoden wordt afgeraden en mag niet worden gebruikt bij het maken van een `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Een bron taal opgeven in Java

In dit voor beeld wordt de bron taal expliciet aangelegd bij het maken van een nieuwe `SpeechRecognizer`.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

In dit voor beeld wordt de bron taal gegeven met behulp van `SourceLanguageConfig`. Vervolgens wordt de `sourceLanguageConfig` door gegeven als een para meter bij het maken van een nieuwe `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

In dit voor beeld worden de bron taal en het aangepaste eind punt voorzien van `SourceLanguageConfig`. Vervolgens wordt de `sourceLanguageConfig` door gegeven als een para meter bij het maken van een nieuwe `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` en `setEndpointId` zijn afgeschafte methoden van de `SpeechConfig` C++ -klasse in en Java. Het gebruik van deze methoden wordt afgeraden en mag niet worden gebruikt bij het maken van een `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>De bron taal in python opgeven

De eerste stap bestaat uit het maken van een `speech_config`:

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

Geef vervolgens de bron taal van uw audio op met `speech_recognition_language`:

```Python
speech_config.speech_recognition_language="de-DE"
```

Als u een aangepast model gebruikt voor herkenning, kunt u het eind punt opgeven met `endpoint_id`:

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Een bron taal opgeven in Java script

De eerste stap bestaat uit het maken van een `SpeechConfig`:

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

De eerste stap bestaat uit het maken van een `speechConfig`:

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
