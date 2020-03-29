---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/21/2020
ms.author: dapine
ms.openlocfilehash: 9b4317064196c4ea3d761fd1a0bd43a764054fe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77563289"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Spraak-naar-tekst of aangepaste spraak-naar-tekst

De container biedt websocket-gebaseerde queryeindpunt API's, die toegankelijk zijn via de [SpraakSDK.](../index.yml) Standaard maakt de Speech SDK gebruik van online spraakdiensten. Als u de container wilt gebruiken, moet u de initialisatiemethode wijzigen.

> [!TIP]
> Wanneer u de Spraak-SDK gebruikt met containers, hoeft u de abonnementssleutel azure speech-bron [of een verificatietoken aan toonder](../rest-speech-to-text.md#authentication)niet op te geven.

Zie de onderstaande voorbeelden.

# <a name="c"></a>[C #](#tab/csharp)

Wijziging van het gebruik van deze Azure-cloud initialisatie-oproep:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

aan deze oproep met behulp van de [containerhost:](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

Wijziging van het gebruik van deze Azure-cloud initialisatie-oproep:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

aan deze oproep met behulp van de [containerhost:](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
