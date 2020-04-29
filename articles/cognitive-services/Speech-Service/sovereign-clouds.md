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
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78228096"
---
# <a name="speech-services-with-sovereign-clouds"></a>Spraak Services met soevereine Clouds

## <a name="azure-government-united-states"></a>Azure Government (Verenigde Staten)

Alleen Amerikaanse federale, staats-, lokale en tribale-overheden en hun partners hebben toegang tot deze specifieke instantie met bewerkingen die worden beheerd door gescreende Amerikaanse staats burgers.
- Regio's: US Gov-Virginia
- SR in SpeechSDK:*config. FromHost ("WSS://Virginia.STT.speech.Azure.us", "\<Your-Key\>");*
- TTS in SpeechSDK: *config. FromHost ("https[]()://Virginia.TTS.speech.Azure.us", "\<Your-Key\>");*
- Verificatie tokens: https[]()://Virginia.API.Cognitive.Microsoft.us/STS/v1.0/issueToken
- Azure-portal:https://portal.azure.us  
- Custom Speech portal:https://virginia.cris.azure.us/Home/CustomSpeech
- Beschik bare Sku's: S0
- Ondersteunde functies:
  - Spraak naar tekst
  - Custom Speech (akoestische/taal aanpassing)
  - Tekst naar spraak
  - Spraak conversie
- Niet-ondersteunde functies
  - Aangepaste spraak
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
- SR in SpeechSDK: *config. FromHost ("WSS://chinaeast2.STT.speech.Azure.cn", "\<Your-Key\>");*
- TTS in SpeechSDK: *config. FromHost ("https[]()://chinaeast2.TTS.speech.Azure.cn", "\<Your-Key\>");*
- Verificatie tokens: https[]()://chinaeast2.API.Cognitive.Azure.cn/STS/v1.0/issueToken
- Azure-portal:https://portal.azure.cn
- Custom Speech portal:https://speech.azure.cn/CustomSpeech
- Beschik bare Sku's: S0
- Ondersteunde functies:
  - Spraak naar tekst
  - Custom Speech (akoestische/taal aanpassing)
  - Tekst naar spraak
  - Spraak conversie
- Niet-ondersteunde functies
  - Aangepaste spraak
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

