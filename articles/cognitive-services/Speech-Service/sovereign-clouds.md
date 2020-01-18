---
title: Soevereine Clouds-Speech-Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van soevereine Clouds
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: 3b96ded52aa2148df9e96d6f1e878d50b821abab
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170466"
---
# <a name="speech-services-with-sovereign-clouds"></a>Spraak Services met soevereine Clouds

## <a name="azure-government-united-states"></a>Azure Government (Verenigde Staten)

Alleen federale, staats-, lokale en stammenoverheden in de VS en hun partners hebben toegang tot deze gespecialiseerde instantie met werkzaamheden die worden bepaald door gescreende Amerikaanse staatsburgers.
- Regio's: US Gov-Virginia
- SR in SpeechSDK:*config. FromHost ("WSS://Virginia.STT.speech.Azure.us", "\<uw-sleutel\>");*
- TTS in SpeechSDK: *config. FromHost ("https[]()://Virginia.TTS.speech.Azure.us", "\<uw-sleutel\>");*
- Verificatie tokens: https[]()://Virginia.API.Cognitive.Microsoft.us/STS/v1.0/issueToken
- Azure-portal: https://portal.azure.us  
- Custom Speech portal: https://virginia.cris.azure.us/Home/CustomSpeech
- Beschik bare Sku's: S0
- Ondersteunde functies:
  - Spraak naar tekst
  - Custom Speech (akoestische/taal aanpassing)
  - Tekst naar spraak
  - Spraak conversie
- Niet-ondersteunde functies
  - Custom Voice
  - Neural stemmen voor tekst naar spraak
- Ondersteunde land instellingen: de land instellingen voor de volgende talen worden ondersteund.
  - Arabisch (AR-*)
  - Chinees (ZH-*)
  - Engels (en-*)
  - Frans (FR-*)
  - Duits (de-*)
  - Hindi
  - Koreaans
  - Russisch
  - Spaans (ES-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure China

In China, een Azure-Data Center met directe toegang tot China Mobile, China Telecom, China Unicom en een ander primair netwerk met vervoerder backbone, voor Chinese gebruikers om een snelle en stabiele lokale netwerk toegangs ervaring te bieden.
- Regio's: China-oost 2 (Shanghai)
- SR in SpeechSDK: *config. FromHost ("WSS://chinaeast2.STT.speech.Azure.cn", "\<uw-sleutel\>");*
- TTS in SpeechSDK: *config. FromHost ("https[]()://chinaeast2.TTS.speech.Azure.cn", "\<uw-sleutel\>");*
- Verificatie tokens: https[]()://chinaeast2.API.Cognitive.Microsoft.cn/STS/v1.0/issueToken
- Azure-portal: https://portal.azure.cn
- Custom Speech portal: https://chinaeast2.cris.azure.cn/Home/CustomSpeech
- Beschik bare Sku's: S0
- Ondersteunde functies:
  - Spraak naar tekst
  - Custom Speech (akoestische/taal aanpassing)
  - Tekst naar spraak
  - Spraak conversie
- Niet-ondersteunde functies
  - Custom Voice
  - Neural stemmen voor tekst naar spraak
- Ondersteunde land instellingen: de land instellingen voor de volgende talen worden ondersteund.
  - Arabisch (AR-*)
  - Chinees (ZH-*)
  - Engels (en-*)
  - Frans (FR-*)
  - Duits (de-*)
  - Hindi
  - Koreaans
  - Russisch
  - Spaans (ES-*)

