---
title: SpraakSDK-geheugengebruik bijhouden - Spraakservice
titleSuffix: Azure Cognitive Services
description: De Speech Service SDK ondersteunt tal van programmeertalen voor spraak-naar-tekst en tekst-naar-spraak conversie, samen met spraakvertaling. In dit artikel wordt gesproken over hulpprogramma's voor geheugenbeheer die in de SDK zijn ingebouwd.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456430"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Het geheugengebruik van Speech SDK bijhouden

De Speech SDK is gebaseerd op een native code base die wordt geprojecteerd in meerdere programmeertalen door middel van een reeks interoperabiliteitslagen. Elke taalspecifieke projectie heeft idiomatisch correcte eigenschappen om de objectlevenscyclus te beheren. Daarnaast bevat de Speech SDK hulpprogramma's voor geheugenbeheer om het gebruik van resources bij te houden met objectlogboekregistratie en objectlimieten. 

## <a name="how-to-read-object-logs"></a>Objectlogboeken lezen

Als [SpraakSDK-logboekregistratie is ingeschakeld,](how-to-use-logging.md)worden trackingtags uitgezonden om historische objectobservatie in te schakelen. Deze tags omvatten: 

* `TrackHandle` of `StopTracking` 
* Het objecttype
* Het huidige aantal objecten dat het type object bijhoudt en het huidige aantal dat wordt bijgehouden.

Hier is een voorbeeldlogboek: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Een waarschuwingsdrempel instellen

U hebt de optie om een waarschuwingsdrempel te maken en als die drempelwaarde wordt overschreden (ervan uitgaande dat logboekregistratie is ingeschakeld), wordt een waarschuwingsbericht geregistreerd. Het waarschuwingsbericht bevat een dump van alle objecten die bestaan, samen met hun telling. Deze informatie kan worden gebruikt om problemen beter te begrijpen. 

Als u een waarschuwingsdrempel wilt inschakelen, moet deze op een `SpeechConfig` object worden opgegeven. Dit object wordt gecontroleerd wanneer een nieuwe herkenningwordt gemaakt. Laten we in de volgende voorbeelden aannemen dat u `SpeechConfig` een `config`instantie hebt gemaakt met de naam:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> De standaardwaarde voor deze eigenschap is 10.000.

## <a name="set-an-error-threshold"></a>Een foutdrempel instellen 

Met de SpraakSDK u het maximum aantal objecten instellen dat op een bepaald moment is toegestaan. Als deze instelling is ingeschakeld, mislukken pogingen om nieuwe herkenningsobjecten te maken wanneer het maximale aantal wordt geraakt. Bestaande objecten blijven werken.

Hier is een voorbeeldfout:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Als u een foutdrempel wilt inschakelen, moet deze op een `SpeechConfig` object worden opgegeven. Dit object wordt gecontroleerd wanneer een nieuwe herkenningwordt gemaakt. Laten we in de volgende voorbeelden aannemen dat u `SpeechConfig` een `config`instantie hebt gemaakt met de naam:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> De standaardwaarde voor deze eigenschap is de `size_t` platformspecifieke maximumwaarde voor een gegevenstype. Een typische herkenning verbruikt tussen de 7 en 10 interne objecten.

## <a name="next-steps"></a>Volgende stappen

* [Uw proefabonnement van spraakservice aanschaffen](get-started.md)
* [Meer informatie over het herkennen van spraak met een microfoon](quickstarts/speech-to-text-from-microphone.md)