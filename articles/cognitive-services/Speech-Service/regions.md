---
title: Regio's-spraak service
titleSuffix: Azure Cognitive Services
description: Verwijzing voor regio's van de spraak service.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 469dab093ed7a62171d232695af3258cc874b5f3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481318"
---
# <a name="speech-service-supported-regions"></a>Ondersteunde regio's voor spraak Services

Met de speech-service kan uw toepassing audio naar tekst omzetten, spraak omzetting uitvoeren en tekst naar spraak converteren. De service is beschikbaar in meerdere regio's met unieke eind punten voor de Speech SDK en REST Api's.

Zorg ervoor dat u het eind punt gebruikt dat overeenkomt met de regio voor uw abonnement.

## <a name="speech-sdk"></a>Speech-SDK

In de [Speech SDK](speech-sdk.md)worden regio's opgegeven als een teken reeks (bijvoorbeeld als een para meter voor het `SpeechConfig.FromSubscription` in de Speech-SDK C#voor).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Spraak naar tekst, tekst-naar-spraak en omzetting

De Speech SDK is beschikbaar in deze regio's voor **spraak herkenning**, **tekst naar spraak**en **vertaling**:

  Regio | Speech SDK-para meter | Portal voor spraak aanpassing
 ------|-------|--------
 US - west | `westus` | https://westus.cris.ai
 US - west 2 | `westus2` | https://westus2.cris.ai
 US - oost | `eastus` | https://eastus.cris.ai
 US - oost 2 | `eastus2` | https://eastus2.cris.ai
 US - centraal | `centralus` | https://centralus.cris.ai
 VS - noord-centraal | `northcentralus` | https://northcentralus.cris.ai
 US - zuid-centraal | `southcentralus` | https://southcentralus.cris.ai
 India - centraal | `centralindia` | https://centralindia.cris.ai
 Azië - oost | `eastasia` | https://eastasia.cris.ai
 Azië - zuidoost | `southeastasia` | https://southeastasia.cris.ai
 Japan - Oost | `japaneast` | https://japaneast.cris.ai
 Korea - centraal | `koreacentral` | https://koreacentral.cris.ai
 Australië - oost | `australiaeast` | https://australiaeast.cris.ai
 Canada - centraal | `canadacentral` | https://canadacentral.cris.ai
 Europa - noord | `northeurope` | https://northeurope.cris.ai
 Europa -west | `westeurope` | https://westeurope.cris.ai
 VK - zuid | `uksouth` | https://uksouth.cris.ai
 Frankrijk - centraal | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Intentie herkenning

Beschik bare regio's voor **intentie herkenning** via de Speech SDK zijn de volgende:

 Wereld wijde regio | Regio | Speech SDK-para meter
 ------|-------|--------
 Azië | Azië - oost | `eastasia`
 Azië | Azië - zuidoost | `southeastasia`
 Australië | Australië - oost | `australiaeast`
 Europa | Europa - noord | `northeurope`
 Europa | Europa -west | `westeurope`
 Noord-Amerika | US - oost | `eastus`
 Noord-Amerika | US - oost 2 | `eastus2`
 Noord-Amerika | US - zuid-centraal | `southcentralus`
 Noord-Amerika | US - west-centraal | `westcentralus`
 Noord-Amerika | US - west | `westus`
 Noord-Amerika | US - west 2 | `westus2`
 Zuid-Amerika | Brazilië - zuid | `brazilsouth`

Dit is een subset van de publicatie regio's die worden ondersteund door de [Language Understanding-service (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Spraak assistenten

De [spraak-SDK](speech-sdk.md) ondersteunt de mogelijkheden van de **Voice Assistant** in deze regio's:

Regio | Speech SDK-para meter
-------|---------------------
US - west | `westus`
US - west 2 | `westus2`
US - oost | `eastus`
US - oost 2 | `eastus2`
Europa -west | `westeurope`
Europa - noord | `northeurope`
Azië - zuidoost | `southeastasia`

## <a name="rest-apis"></a>REST API's

De spraak service stelt ook REST-eind punten beschikbaar voor aanvragen voor spraak naar tekst en tekst naar spraak.

### <a name="speech-to-text"></a>Spraak naar tekst

Zie [spraak-naar-tekst-rest API](rest-speech-to-text.md)voor naslag informatie over spraak naar tekst.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Tekst naar spraak

Zie [tekst-naar-spraak-rest API](rest-text-to-speech.md)voor naslag informatie over tekst naar spraak.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]