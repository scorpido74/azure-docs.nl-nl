---
title: Terminologie-Azure
description: Dit artikel bevat een overzicht van live video analyses op IoT Edge terminologie.
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: 51fcc962f6546d727dbbc5e7ff62dc9c4e5677af
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261042"
---
# <a name="terminology"></a>Terminologie

Dit artikel bevat een overzicht van de terminologie die betrekking heeft op [Live video Analytics op IOT Edge](overview.md).

## <a name="azure-media-services"></a>Azure Media Services

Azure Media Services is een platform voor Cloud media waarmee u media oplossingen kunt bouwen. Meer informatie hierover vindt u in de [Azure Media Services](../latest/media-services-overview.md) documentatie.

## <a name="asset"></a>Asset

[Asset](../latest/assets-concept.md) is een entiteit in azure Media Services die wordt toegewezen aan een BLOB-container in het Azure Storage-account dat is gekoppeld aan een Media Services-account. Alle bestanden die zijn gekoppeld aan een Asset, worden opgeslagen als blobs in die container terwijl Media Services de meta gegevens (bijvoorbeeld een naam, beschrijving, aanmaak tijd) bevat die aan de Asset zijn gekoppeld.

Live video Analytics op IoT Edge kan assets maken en/of gegevens toevoegen aan bestaande assets. Hierdoor kunnen de scenario's van doorlopend en op gebeurtenissen gebaseerde video-opname en-afspelen (met video-opname op het apparaat aan de rand, het vastleggen van Azure Media Services en het afspelen via bestaande Azure Media Services streaming-mogelijkheden) worden uitgevoerd.

## <a name="streaming"></a>Streaming

Als u video op een mobiel apparaat hebt bekeken uit services zoals Netflix, YouTube en anderen, hebt u ervaring met het streamen van video. Het afspelen begint binnenkort nadat u ' afspelen ' hebt bereikt (als u voldoende band breedte hebt) en u kunt teruggaan naar de tijd lijn van de video. Met streaming is het verstandig om alleen het gedeelte van de weer te geven video te leveren en de viewer de video te laten afspelen terwijl de gegevens nog steeds van een server naar de Play-client worden overgebracht. In de context van Azure Media Services verwijst [streaming](https://en.wikipedia.org/wiki/Streaming_media) naar het proces van het leveren van Media van [Azure Media Services](https://docs.microsoft.com/azure/media-services/azure-media-player/azure-media-player-overview) aan een streaming-client (bijvoorbeeld Azure Media Player). U kunt Azure Media Services gebruiken om video naar clients te streamen met behulp van industrie standaard, op HTTP gebaseerde protocollen voor mediastreaming zoals [http live streaming (HLS)](https://developer.apple.com/streaming/) en [MPEG-Dash](https://dashif.org/about/). HLS wordt ondersteund door Azure Media Player, en webonderdelen zoals [JW Player](https://www.jwplayer.com/), [HLS. js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), [Google Shake Player](https://github.com/google/shaka-player), of u kunt systeem eigen weer geven in mobiele apps met behulp van de [Exoplayer](https://github.com/google/ExoPlayer) van Android en de [AV Foundation](https://developer.apple.com/av-foundation/)van Ios. MPEG-DASH wordt ook ondersteund door Azure Media Player, [een lijst met clients op deze pagina zoeken](https://dashif.org/clients/). 

Door [Media Graph](#media-graph)s te gebruiken om Video's op te nemen in een asset in azure Media Services, kunt u Media Services streaming-mogelijkheid gebruiken om video-streams in HLS en Dash te leveren. Meer informatie hierover vindt u in het artikel [video afspelen](video-playback-concept.md) .

## <a name="recording"></a>Opnemen

In de context van een video beheersysteem voor beveiligings camera's verwijst de video-opname naar het proces van het vastleggen van video van de camera's en het opslaan in een bestand (of bestanden) voor de volgende weer gave via mobiele en browser-apps. Video-opname kan worden gecategoriseerd in [doorlopende video-opnamen](continuous-video-recording-concept.md) en [op gebeurtenissen gebaseerde video-opnamen](event-based-video-recording-concept.md). Deze worden gedetailleerd beschreven in de concept pagina voor het [opnemen van video](video-recording-concept.md) .

## <a name="media-graph"></a>Media grafiek

Met [Media Graph](media-graph-concept.md) kunt u bepalen waar media moeten worden vastgelegd, hoe deze moeten worden verwerkt en waar de resultaten moeten worden bezorgd. Hiermee kunt u een grafiek definiëren die bestaat uit bronnen, processors en Sink-knoop punten. Daarom biedt u de mogelijkheid om live video Analytics-toepassingen te bouwen. Media Graph wordt gedetailleerd beschreven op de concept pagina van media Graph.

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) verwijst naar het real-time streaming-protocol. Het is een protocol op toepassings niveau voor controle over de levering van gegevens met realtime-eigenschappen. RTSP biedt een uitbreidbaar Framework voor het inschakelen van beheerde, on-demand levering van real-time gegevens, zoals audio en video. 

## <a name="vms"></a>VIRTUELE

[Vm's](https://en.wikipedia.org/wiki/Video_management_system) verwijzen naar een video beheersysteem. Dergelijke systemen worden gebruikt voor het configureren en beheren van CCTV-camera's, het vastleggen en opnemen van Video's. Deze systemen bieden ook client toepassingen voor het afspelen van de opgenomen video

## <a name="next-steps"></a>Volgende stappen

[Media grafieken](media-graph-concept.md)
