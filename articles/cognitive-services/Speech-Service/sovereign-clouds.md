---
title: Soevereine wolken - Spraakdienst
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van Soevereine Wolken
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228096"
---
# <a name="speech-services-with-sovereign-clouds"></a>Toespraakdiensten met soevereine wolken

## <a name="azure-government-united-states"></a>Azure-regering (Verenigde Staten)

Alleen amerikaanse federale, staats-, lokale en tribale regeringen en hun partners hebben toegang tot dit specifieke exemplaar met operaties die worden gecontroleerd door gescreende Amerikaanse burgers.
- Regio's: US Gov Virginia
- SR in SpeechSDK:*config. FromHost("wss://virginia.stt.speech.azure.us",\<" your-key\>");*
- TTS in SpeechSDK: *config. FromHost("https[]()://virginia.tts.speech.azure.us",\<"\>uw sleutel ");*
- Verificatietokens:[]()https ://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Azure-portal:https://portal.azure.us  
- Aangepaste spraakportal:https://virginia.cris.azure.us/Home/CustomSpeech
- Beschikbare SKU's: S0
- Ondersteunde functies:
  - Spraak naar tekst
  - Aangepaste spraak (akoestische/taalaanpassing)
  - Tekst naar spraak
  - Spraakvertaler
- Niet-ondersteunde functies
  - Aangepaste stem
  - Neurale stemmen voor tekst-naar-spraak
- Ondersteunde landlocaties: landlocaties voor de volgende talen worden ondersteund.
  - Arabisch (ar-*)
  - Chinees (zh-*)
  - Engels (en-*)
  - Frans (fr-*)
  - Duits (de-*)
  - Hindi
  - Koreaans
  - Russisch
  - Spaans (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure China

Gelegen in China, een Azure datacenter met directe toegang tot China Mobile, China Telecom, China Unicom en andere belangrijke carrier backbone netwerk, voor Chinese gebruikers om high-speed en stabiele lokale netwerktoegang ervaring te bieden.
- Regio's: China East 2 (Shanghai)
- SR in SpeechSDK: *config. FromHost("wss://chinaeast2.stt.speech.azure.cn",\<" your-key\>");*
- TTS in SpeechSDK: *config. FromHost("https[]()://chinaeast2.tts.speech.azure.cn",\<"\>uw sleutel ");*
- Verificatietokens:[]()https ://chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken
- Azure-portal:https://portal.azure.cn
- Aangepaste spraakportal:https://speech.azure.cn/CustomSpeech
- Beschikbare SKU's: S0
- Ondersteunde functies:
  - Spraak naar tekst
  - Aangepaste spraak (akoestische/taalaanpassing)
  - Tekst naar spraak
  - Spraakvertaler
- Niet-ondersteunde functies
  - Aangepaste stem
  - Neurale stemmen voor tekst-naar-spraak
- Ondersteunde landlocaties: landlocaties voor de volgende talen worden ondersteund.
  - Arabisch (ar-*)
  - Chinees (zh-*)
  - Engels (en-*)
  - Frans (fr-*)
  - Duits (de-*)
  - Hindi
  - Koreaans
  - Russisch
  - Spaans (es-*)

