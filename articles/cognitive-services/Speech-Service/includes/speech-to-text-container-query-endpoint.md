---
title: Een query uitvoeren voor het eind punt van een-naar-tekst-container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2d96385f2d2d34d161739c55228220cf28871048
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "74132620"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Spraak-naar-tekst-of Custom Speech-naar-tekst

De container biedt Api's voor query-eind punten op basis van websockets die worden geopend via de [Speech SDK](../index.md). De Speech SDK maakt standaard gebruik van online spraak Services. Als u de container wilt gebruiken, moet u de initialisatie methode wijzigen.

> [!TIP]
> Wanneer u de Speech SDK met containers gebruikt, hoeft u de Azure speech resource [Subscription-sleutel of een verificatie Bearer-token](../rest-speech-to-text.md#authentication)niet op te geven.

Zie de onderstaande voor beelden.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wijzigen van het gebruik van deze Azure-Cloud initialisatie aanroep:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

voor deze aanroep via de container- [host](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet):

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

Wijzigen van het gebruik van deze Azure-Cloud initialisatie aanroep:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

voor deze aanroep via de container- [host](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python):

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
