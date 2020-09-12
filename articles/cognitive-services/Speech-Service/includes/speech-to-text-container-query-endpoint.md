---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 54ccaebd84c6af308ddcfa956add7f84b6e55832
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321012"
---
De container biedt Api's voor query-eind punten op basis van websockets die worden geopend via de [Speech SDK](../index.yml). De Speech SDK maakt standaard gebruik van online spraak Services. Als u de container wilt gebruiken, moet u de initialisatie methode wijzigen.

> [!TIP]
> Wanneer u de Speech SDK met containers gebruikt, hoeft u de Azure speech resource [Subscription-sleutel of een verificatie Bearer-token](../rest-speech-to-text.md#authentication)niet op te geven.

Zie de onderstaande voorbeelden.

# <a name="c"></a>[C#](#tab/csharp)

Wijzigen van het gebruik van deze Azure-Cloud initialisatie aanroep:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

voor deze aanroep via de container- [host](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet):

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

Wijzigen van het gebruik van deze Azure-Cloud initialisatie aanroep:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

---
