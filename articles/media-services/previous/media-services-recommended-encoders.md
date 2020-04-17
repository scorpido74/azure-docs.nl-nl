---
title: Meer informatie over encoders die worden aanbevolen door Azure Media Services | Microsoft Documenten
description: In dit artikel worden allecoderingen weergegeven die worden aanbevolen door Azure Media Services.
services: media-services
keywords: codering;encoders;media
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 3c42070525fc60e45b976620513929c3d5a32341
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535091"
---
# <a name="recommended-on-premises-encoders"></a>Aanbevolen on-premises coderingsprogramma's

Wanneer u live streamt met Azure Media Services, u opgeven hoe u wilt dat uw kanaal de invoerstream ontvangt. Als u ervoor kiest om een on-premises encoder te gebruiken met een live coderingskanaal, moet uw encoder een hoogwaardige single-bitrate stream als uitvoer pushen. Als u ervoor kiest om een on-premises encoder te gebruiken met een pass through channel, moet uw encoder een multi-bitrate stream als output met alle gewenste uitgangskwaliteiten duwen. Zie [Live streaming met on-premises encoders](media-services-live-streaming-with-onprem-encoders.md)voor meer informatie.

## <a name="encoder-requirements"></a>Encoder-eisen

Encoders moeten TLS 1.2 ondersteunen bij het gebruik van HTTPS- of RTMPS-protocollen.

## <a name="live-encoders-that-output-rtmp"></a>Live-encoders die RTMP uiteren 

Azure Media Services raadt aan een van de volgende live-encoders te gebruiken die RTMP als uitvoer hebben:

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast (versie 13.0.2 of hoger vanwege de TLS 1.2 vereiste)

  Encoders moeten TLS 1.2 ondersteunen bij het gebruik van RTMPS-protocollen.
- Teradek Slice 756
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Live encoders die gefragmenteerde MP4 uitte 

Azure Media Services raadt aan om een van de volgende live-encoders te gebruiken die multibitrate fragmented-MP4 (Smooth Streaming) als uitvoer hebben:

- Media Excel Hero Live en Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (versie 2.14.15 en hoger vanwege de TLS 1.2-vereiste)

  Encoders moeten TLS 1.2 ondersteunen bij het gebruik van HTTPS-protocollen.
- Envivio 4Caster C4 Gen III
- Stel je Communicatie Selenio MCP3

> [!NOTE]
> Een live encoder kan een single-bitrate stream naar een pass through-kanaal sturen, maar deze configuratie wordt niet aanbevolen omdat het geen adaptieve bitrate streaming naar de client toestaat.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Hoe een on-premises encoder partner te worden

Als azure media services on-premises encoderpartner promoot Media Services uw product door uw encoder aan zakelijke klanten aan te bevelen. Om een on-premises encoderpartner te worden, moet u de compatibiliteit van uw on-premises encoder met Media Services verifiëren. Voer hiervoor de volgende verificaties uit:

Kanaalverificatie doorlopen
1. Uw Azure Media Services-account maken of bezoeken
2. Een **doorgeefkanaal** maken en starten
3. Configureer uw encoder om een multi-bitrate live stream te duwen.
4. Een gepubliceerd live-evenement maken
5. Voer uw levende encoder ongeveer 10 minuten uit
6. Stop het live-evenement
7. Maak, start een streaming-eindpunt, gebruik een speler zoals [Azure Media Player](https://aka.ms/azuremediaplayer) om de gearchiveerde asset te bekijken om ervoor te zorgen dat afspelen geen zichtbare glitches heeft voor alle kwaliteitsniveaus (Of als alternatief kijken en valideren via de Preview-URL tijdens de live sessie voor stap 6)
8. Neem de Asset ID, gepubliceerde streaming URL voor het live archief, en de instellingen en versie die worden gebruikt van uw live encoder
9. De kanaalstatus opnieuw instellen na het maken van elk voorbeeld
10. Herhaal stap 3 tot en met 9 voor alle configuraties die worden ondersteund door uw encoder (met en zonder advertentiesignalering/bijschriften/verschillende coderingssnelheden)

Verificatie van live coderingskanaal
1. Uw Azure Media Services-account maken of bezoeken
2. Een live **coderingskanaal** maken en starten
3. Configureer uw encoder om een single-bitrate live stream te duwen.
4. Een gepubliceerd live-evenement maken
5. Voer uw levende encoder ongeveer 10 minuten uit
6. Stop het live-evenement
7. Maak, start een streaming-eindpunt, gebruik een speler zoals [Azure Media Player](https://aka.ms/azuremediaplayer) om de gearchiveerde asset te bekijken om ervoor te zorgen dat afspelen geen zichtbare glitches heeft voor alle kwaliteitsniveaus (Of als alternatief kijken en valideren via de Preview-URL tijdens de live sessie voor stap 6)
8. Neem de Asset ID, gepubliceerde streaming URL voor het live archief, en de instellingen en versie die worden gebruikt van uw live encoder
9. De kanaalstatus opnieuw instellen na het maken van elk voorbeeld
10. Herhaal stap 3 tot en met 9 voor alle configuraties die worden ondersteund door uw encoder (met en zonder advertentiesignalering/bijschriften/verschillende coderingssnelheden)

Langverificatie
1. Uw Azure Media Services-account maken of bezoeken
2. Een **doorgeefkanaal** maken en starten
3. Configureer uw encoder om een multi-bitrate live stream te duwen.
4. Een gepubliceerd live-evenement maken
5. Voer uw levende encoder voor een week of langer
6. Gebruik een speler zoals [Azure Media Player](https://aka.ms/azuremediaplayer) om de live streaming van tijd tot tijd (of gearchiveerde asset) te bekijken om ervoor te zorgen dat afspelen geen zichtbare glitches heeft
7. Stop het live-evenement
8. Neem de Asset ID, gepubliceerde streaming URL voor het live archief, en de instellingen en versie die worden gebruikt van uw live encoder

Tot slot, stuur uw opgenomen instellingen en live amsstreaming@microsoft.comarchief parameters naar Media Services door te e-mailen . Na ontvangst voert Media Services verificatietests uit op de monsters van uw levende encoder. U contact opnemen met de Media Services met vragen over dit proces.
