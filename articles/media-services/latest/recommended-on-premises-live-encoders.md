---
title: Live streaming encoders aanbevolen door Media Services - Azure | Microsoft Documenten
description: Meer informatie over live streaming on-premises encoders aanbevolen door Media Services
services: media-services
keywords: codering;encoders;media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 5e16f1fb948ddb435c5002c16125b36fa61d50a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336250"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>Getest on-premises live streaming encoders

In Azure Media Services vertegenwoordigt een [Live Event](https://docs.microsoft.com/rest/api/media/liveevents) (kanaal) een pijplijn voor het verwerken van live streaming content. Het Live Event ontvangt op twee manieren live-invoerstreams.

* Een on-premises live-encoder stuurt een multi-bitrate RTMP- of Smooth Streaming -stream (gefragmenteerde MP4) naar het Live-evenement dat niet is ingeschakeld om live codering met Media Services uit te voeren. De ingenomen streams passeren Live Events zonder verdere verwerking. Deze methode wordt **pass-through**genoemd. We raden de live-encoder aan om multibitrate streams te verzenden in plaats van een single-bitrate stream naar een pass-through live evenement om adaptieve bitrate streaming naar de client mogelijk te maken. 

    Als u multibitrates streams gebruikt voor de pass-through live gebeurtenis, moeten de video GOP-grootte en de videofragmenten op verschillende bitrates worden gesynchroniseerd om onverwacht gedrag aan de afspeelkant te voorkomen.

  > [!TIP]
  > Het gebruik van een pass-through methode is de meest economische manier om live streaming te doen.
 
* Een on-premises live-encoder stuurt een stream met één bitrate naar het Live-evenement die is ingeschakeld om live codering met Media Services uit te voeren in een van de volgende indelingen: RTMP of Smooth Streaming (gefragmenteerde MP4). Het Live Event voert vervolgens live codering uit van de binnenkomende single-bitrate stream naar een multi-bitrate (adaptieve) videostream.

Dit artikel bespreekt geteste on-premises live streaming encoders. Zie [uw on-premises encoder controleren](become-on-premises-encoder-partner.md) of u instructies hebt over het verifiëren van uw on-premises encoder

Zie Live streaming met Media Services [v3](live-streaming-overview.md)voor gedetailleerde informatie over live-codering met Media Services.

## <a name="encoder-requirements"></a>Encoder-eisen

Encoders moeten TLS 1.2 ondersteunen bij het gebruik van HTTPS- of RTMPS-protocollen.

## <a name="live-encoders-that-output-rtmp"></a>Live-encoders die RTMP uiteren

Media Services raadt het gebruik aan van een van de volgende live-encoders met RTMP als uitvoer. De ondersteunde URL-schema's zijn `rtmp://` of `rtmps://`.

Bij het streamen via RTMP controleert u de instellingen voor de firewall en/of proxy om te zien of de uitgaande TCP-poorten 1935 en 1936 open zijn.<br/><br/>
Bij het streamen via RTMPS controleert u de instellingen voor de firewall en/of proxy om te zien of de uitgaande TCP-poorten 2935 en 2936 open zijn.

> [!NOTE]
> Encoders moeten TLS 1.2 ondersteunen bij het gebruik van RTMPS-protocollen.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (versie 2.14.15 en hoger)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast (versie 13.0.2 of hoger vanwege de TLS 1.2 vereiste)
- Telestream Wirecast S (alleen RTMP wordt ondersteund)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro (GoPro)](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 en Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Live encoders die gefragmenteerde MP4 uitte

Media Services raadt aan om een van de volgende live-encoders te gebruiken die multibitrate Smooth Streaming (gefragmenteerde MP4) als uitvoer hebben. De ondersteunde URL-schema's zijn `http://` of `https://`.

> [!NOTE]
> Encoders moeten TLS 1.2 ondersteunen bij het gebruik van HTTPS-protocollen.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (versie 2.14.15 en hoger vanwege de TLS 1.2-vereiste)
- Envivio 4Caster C4 Gen III 
- Stel je Communicatie Selenio MCP3
- Media Excel Hero Live en Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Als u live-evenementen streamt in meerdere talen (bijvoorbeeld één Engels audiospoor en één Spaans audiospoor), u dit bereiken met de Media Excel live-encoder die is geconfigureerd om de live-feed naar een live-evenement te verzenden.

## <a name="configuring-on-premises-live-encoder-settings"></a>On-premises instellingen voor live-encoder configureren

Zie Vergelijking van [typen live-evenementen](live-event-types-comparison.md)voor informatie over de instellingen die geldig zijn voor uw live-evenementtype.

### <a name="playback-requirements"></a>Vereisten voor afspelen

Om inhoud af te spelen, moet zowel een audio- als videostream aanwezig zijn. Het afspelen van de videostream wordt niet ondersteund.

### <a name="configuration-tips"></a>Configuratietips

- Gebruik indien mogelijk een vaste internetverbinding.
- Wanneer u de bandbreedtevereisten bepaalt, verdubbelt u de streamingbitrates. Hoewel deze eenvoudige regel niet verplicht is, helpt deze eenvoudige regel de impact van netwerkcongestie te beperken.
- Sluit bij het gebruik van softwaregebaseerde encoders onnodige programma's af.
- Het wijzigen van uw encoderconfiguratie nadat deze is begonnen met duwen heeft negatieve effecten op de gebeurtenis. Configuratiewijzigingen kunnen ertoe leiden dat de gebeurtenis instabiel wordt. 
- Zorg ervoor dat u uzelf voldoende tijd geeft om uw evenement op te zetten. Voor grootschalige evenementen raden we aan om de installatie een uur voor uw evenement te starten.
- Gebruik de H.264-video- en OC-audiocodec-uitvoer.
- Zorg ervoor dat er een hoofdframe of GOP-tijdelijke uitlijning is tussen videokwaliteiten.
- Zorg ervoor dat er een unieke streamnaam is voor elke videokwaliteit.
- Gebruik strikte CBR-codering aanbevolen voor optimale adaptieve bitrate prestaties.

> [!IMPORTANT]
> Bekijk de fysieke conditie van de machine (CPU / Geheugen / etc) als het uploaden van fragmenten naar de cloud gaat CPU en IO operaties. Als u instellingen in de encoder wijzigt, moet u de kanalen / live gebeurtenis opnieuw instellen om de wijziging van kracht te laten worden.

## <a name="see-also"></a>Zie ook

[Live streaming met Media Services v3](live-streaming-overview.md)

## <a name="next-steps"></a>Volgende stappen

[Hoe u uw encoder verifiëren](become-on-premises-encoder-partner.md)
