---
title: Regio's-spraak service
titleSuffix: Azure Cognitive Services
description: Een lijst met beschik bare regio's en eind punten voor de spraak service, inclusief spraak naar tekst, tekst naar spraak en spraak omzetting.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: f1f425a6e3c66a72c7632cfea16d78ea6cc0d319
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201149"
---
# <a name="speech-service-supported-regions"></a>Ondersteunde regio's voor spraak Services

De spraak-service kunt uw toepassing audio naar tekst wilt converteren, voert u spraakomzetting en converteren tekst naar spraak. De service is beschikbaar in meerdere regio's met unieke eindpunten voor de spraak-SDK en REST-API's.

De spraak Portal om aangepaste configuraties uit te voeren op uw spraak ervaring voor alle regio's is hier beschikbaar: https://speech.microsoft.com

Zorg ervoor dat de oproep overeenkomt met de regio voor uw abonnement voor het aanroepen van uw spraak service.

## <a name="speech-sdk"></a>Speech-SDK

In de [Speech SDK](speech-sdk.md)worden regio's opgegeven als een teken reeks (bijvoorbeeld als een para meter voor het `SpeechConfig.FromSubscription` in de Speech-SDK C#voor).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Spraak naar tekst, tekst-naar-spraak en omzetting

De portal voor spraak aanpassing is hier beschikbaar: https://speech.microsoft.com

De speech-service is beschikbaar in deze regio's voor **spraak herkenning**, **tekst naar spraak**en **vertaling**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Als u de [Speech SDK](speech-sdk.md)gebruikt, worden regio's opgegeven met de **regio-id** (bijvoorbeeld als een para meter voor `SpeechConfig.FromSubscription`). Zorg ervoor dat de regio overeenkomt met de regio van uw abonnement.

### <a name="intent-recognition"></a>Intentieherkenning

Beschik bare regio's voor **intentie herkenning** via de Speech SDK zijn de volgende:

| Globale regio | Regio           | Parameter voor spraak-SDK |
| ------------- | ---------------- | -------------------- |
| Azië          | Azië - oost        | `eastasia`           |
| Azië          | Azië - zuidoost   | `southeastasia`      |
| Australië     | Australië - oost   | `australiaeast`      |
| Europa        | Europa - noord     | `northeurope`        |
| Europa        | Europa -west      | `westeurope`         |
| Noord-Amerika | US - oost          | `eastus`             |
| Noord-Amerika | US - oost 2        | `eastus2`            |
| Noord-Amerika | US - zuid-centraal | `southcentralus`     |
| Noord-Amerika | US - west-centraal  | `westcentralus`      |
| Noord-Amerika | US - west          | `westus`             |
| Noord-Amerika | US - west 2        | `westus2`            |
| Zuid-Amerika | Brazilië - zuid     | `brazilsouth`        |

Dit is een subset van de publicatie regio's die worden ondersteund door de [Language Understanding-service (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Spraakassistenten

De [spraak-SDK](speech-sdk.md) ondersteunt de mogelijkheden van de **Voice Assistant** in deze regio's:

| Regio         | Parameter voor spraak-SDK |
| -------------- | -------------------- |
| US - west        | `westus`             |
| US - west 2      | `westus2`            |
| US - oost        | `eastus`             |
| US - oost 2      | `eastus2`            |
| Europa -west    | `westeurope`         |
| Europa - noord   | `northeurope`        |
| Azië - zuidoost | `southeastasia`      |

## <a name="rest-apis"></a>REST API’s

De spraak-service wordt ook aangegeven REST-eindpunten voor spraak-naar-tekst en spraak-aanvragen.

### <a name="speech-to-text"></a>Spraak naar tekst

Zie [spraak-naar-tekst-rest API](rest-speech-to-text.md)voor naslag informatie over spraak naar tekst.

Het eind punt voor de REST API heeft de volgende indeling:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Vervang `<REGION_IDENTIFIER>` door de id die overeenkomt met de regio van uw abonnement uit deze tabel:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> De para meter language moet worden toegevoegd aan de URL om te voor komen dat er een 4xx HTTP-fout wordt ontvangen. De taal die is ingesteld op Amerikaans-Engels met het eind punt vs West is: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Tekst naar spraak

Zie [tekst-naar-spraak-rest API](rest-text-to-speech.md)voor naslag informatie over tekst naar spraak.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
