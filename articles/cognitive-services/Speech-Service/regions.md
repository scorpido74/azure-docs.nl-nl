---
title: Regio's - Spraakservice
titleSuffix: Azure Cognitive Services
description: Een lijst met beschikbare regio's en eindpunten voor de spraakservice, inclusief spraak-naar-tekst, tekst-naar-spraak en spraakvertaling.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 560575ca7f51218e472abecb4319f4a3db69b1ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220466"
---
# <a name="speech-service-supported-regions"></a>Door spraakservice ondersteunde regio's

Met de spraakservice kan uw toepassing audio converteren naar tekst, spraakvertaling uitvoeren en geheime tekst naar spraak uitvoeren. De service is beschikbaar in meerdere regio's met unieke eindpunten voor de Speech SDK en REST API's.

De spraakportal voor het uitvoeren van aangepaste configuraties voor uw spraakervaring voor alle regio's is hier beschikbaar:https://speech.microsoft.com

Controleer de oproep voor het inschakelen van uw spraakservice op de regio voor uw abonnement.

## <a name="speech-sdk"></a>Speech-SDK

In de [SpraakSDK](speech-sdk.md)worden regio's opgegeven als tekenreeks `SpeechConfig.FromSubscription` (bijvoorbeeld als parameter in de SpraakSDK voor C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Spraak-naar-tekst, tekst-naar-spraak en vertaling

De portal voor het aanpassen van spraak is hier beschikbaar:https://speech.microsoft.com

De spraakservice is in deze regio's beschikbaar voor **spraakherkenning,** **tekst-naar-spraak**en **vertaling:**

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Als u de [Spraak-SDK](speech-sdk.md)gebruikt, worden regio's opgegeven door `SpeechConfig.FromSubscription`de **regio-id** (bijvoorbeeld als parameter om ). Controleer of de regio overeenkomt met de regio van uw abonnement.

### <a name="intent-recognition"></a>Intentieherkenning

Beschikbare regio's voor **intentieherkenning** via de SpraakSDK zijn de volgende:

| Regio wereldwijd | Regio           | Regio-id |
| ------------- | ---------------- | -------------------- |
| Azië          | Azië - oost        | `eastasia`           |
| Azië          | Azië - zuidoost   | `southeastasia`      |
| Australië     | Australië - oost   | `australiaeast`      |
| Europa        | Europa - noord     | `northeurope`        |
| Europa        | Europa -west      | `westeurope`         |
| Noord-Amerika | VS - oost          | `eastus`             |
| Noord-Amerika | VS - oost 2        | `eastus2`            |
| Noord-Amerika | VS - zuid-centraal | `southcentralus`     |
| Noord-Amerika | VS - west-centraal  | `westcentralus`      |
| Noord-Amerika | VS - west          | `westus`             |
| Noord-Amerika | VS - west 2        | `westus2`            |
| Zuid-Amerika | Brazilië - zuid     | `brazilsouth`        |

Dit is een subset van de publicatieregio's die worden ondersteund door de [dienst Taalbegrip (LUIS).](/azure/cognitive-services/luis/luis-reference-regions)

### <a name="voice-assistants"></a>Spraakassistenten

De [Speech SDK](speech-sdk.md) ondersteunt spraakassistentmogelijkheden in deze regio's: **voice assistant**

| Regio         | Regio-id |
| -------------- | -------------------- |
| VS - west        | `westus`             |
| VS - west 2      | `westus2`            |
| VS - oost        | `eastus`             |
| VS - oost 2      | `eastus2`            |
| Europa -west    | `westeurope`         |
| Europa - noord   | `northeurope`        |
| Azië - zuidoost | `southeastasia`      |

## <a name="rest-apis"></a>REST-API’s

De spraakservice stelt ook REST-eindpunten bloot voor spraak-naar-tekst- en tekst-naar-spraakverzoeken.

### <a name="speech-to-text"></a>Spraak naar tekst

Zie [Speech-to-text REST API voor](rest-speech-to-text.md)referentiedocumentatie voor spraak naar tekst.

Het eindpunt voor de REST API heeft deze indeling:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Vervang `<REGION_IDENTIFIER>` in deze tabel de id die overeenkomt met de regio van uw abonnement:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> De taalparameter moet aan de URL worden toegevoegd om te voorkomen dat u een HTTP-fout van 4xx ontvangt. De taal die is ingesteld op Amerikaans Engels met `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`behulp van het Eindpunt van west-VS is bijvoorbeeld: .

### <a name="text-to-speech"></a>Tekst naar spraak

Zie [Text-to-speech REST API voor](rest-text-to-speech.md)tekst-naar-spraakreferentie.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
