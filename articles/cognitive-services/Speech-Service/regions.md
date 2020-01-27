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
ms.openlocfilehash: 409ce8b904997f2ab75f70b2138ec5b1e70a0e69
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816660"
---
# <a name="speech-service-supported-regions"></a>Ondersteunde regio's voor spraak Services

De spraak-service kunt uw toepassing audio naar tekst wilt converteren, voert u spraakomzetting en converteren tekst naar spraak. De service is beschikbaar in meerdere regio's met unieke eindpunten voor de spraak-SDK en REST-API's.

Zorg ervoor dat u het eindpunt dat overeenkomt met de regio voor uw abonnement.

## <a name="speech-sdk"></a>Speech-SDK

In de [Speech SDK](speech-sdk.md)worden regio's opgegeven als een teken reeks (bijvoorbeeld als een para meter voor het `SpeechConfig.FromSubscription` in de Speech-SDK C#voor).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Spraak naar tekst, tekst-naar-spraak en omzetting

De Speech SDK is beschikbaar in deze regio's voor **spraak herkenning**, **tekst naar spraak**en **vertaling**:

| Regio           | Parameter voor spraak-SDK | Spraak aanpassing Portal    |
| ---------------- | -------------------- | ------------------------------ |
| US - west          | `westus`             | https://westus.cris.ai         |
| US - west 2        | `westus2`            | https://westus2.cris.ai        |
| VS - oost          | `eastus`             | https://eastus.cris.ai         |
| VS - oost 2        | `eastus2`            | https://eastus2.cris.ai        |
| US - centraal       | `centralus`          | https://centralus.cris.ai      |
| US - noord-centraal | `northcentralus`     | https://northcentralus.cris.ai |
| US - zuid-centraal | `southcentralus`     | https://southcentralus.cris.ai |
| India - centraal    | `centralindia`       | https://centralindia.cris.ai   |
| Azië - oost        | `eastasia`           | https://eastasia.cris.ai       |
| Azië - zuidoost   | `southeastasia`      | https://southeastasia.cris.ai  |
| Japan - Oost       | `japaneast`          | https://japaneast.cris.ai      |
| Korea - centraal    | `koreacentral`       | https://koreacentral.cris.ai   |
| Australië - oost   | `australiaeast`      | https://australiaeast.cris.ai  |
| Canada-Midden   | `canadacentral`      | https://canadacentral.cris.ai  |
| Europa - noord     | `northeurope`        | https://northeurope.cris.ai    |
| Europa - west      | `westeurope`         | https://westeurope.cris.ai     |
| UK - zuid         | `uksouth`            | https://uksouth.cris.ai        |
| Frankrijk - centraal   | `francecentral`      | https://francecentral.cris.ai  |

### <a name="intent-recognition"></a>Intentieherkenning

Beschik bare regio's voor **intentie herkenning** via de Speech SDK zijn de volgende:

| Globale regio | Regio           | Parameter voor spraak-SDK |
| ------------- | ---------------- | -------------------- |
| Azië          | Azië - oost        | `eastasia`           |
| Azië          | Azië - zuidoost   | `southeastasia`      |
| Australië     | Australië - oost   | `australiaeast`      |
| Europa        | Europa - noord     | `northeurope`        |
| Europa        | Europa - west      | `westeurope`         |
| Noord-Amerika | VS - oost          | `eastus`             |
| Noord-Amerika | VS - oost 2        | `eastus2`            |
| Noord-Amerika | US - zuid-centraal | `southcentralus`     |
| Noord-Amerika | US - west-centraal  | `westcentralus`      |
| Noord-Amerika | US - west          | `westus`             |
| Noord-Amerika | US - west 2        | `westus2`            |
| Zuid-Amerika | Brazilië - Zuid     | `brazilsouth`        |

Dit is een subset van de publicatie regio's die worden ondersteund door de [Language Understanding-service (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Spraakassistenten

De [spraak-SDK](speech-sdk.md) ondersteunt de mogelijkheden van de **Voice Assistant** in deze regio's:

| Regio         | Parameter voor spraak-SDK |
| -------------- | -------------------- |
| US - west        | `westus`             |
| US - west 2      | `westus2`            |
| VS - oost        | `eastus`             |
| VS - oost 2      | `eastus2`            |
| Europa - west    | `westeurope`         |
| Europa - noord   | `northeurope`        |
| Azië - zuidoost | `southeastasia`      |

## <a name="rest-apis"></a>REST API's

De spraak-service wordt ook aangegeven REST-eindpunten voor spraak-naar-tekst en spraak-aanvragen.

### <a name="speech-to-text"></a>Spraak naar tekst

Zie [spraak-naar-tekst-rest API](rest-speech-to-text.md)voor naslag informatie over spraak naar tekst.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Tekst naar spraak

Zie [tekst-naar-spraak-rest API](rest-text-to-speech.md)voor naslag informatie over tekst naar spraak.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
