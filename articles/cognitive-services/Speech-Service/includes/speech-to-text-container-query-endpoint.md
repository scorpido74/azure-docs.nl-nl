---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c029165a4994e5e3f73399fb00a1775b67128e44
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879009"
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
