---
title: Overzicht van live streaming met Azure Media Services v3 | Microsoft Documenten
description: Dit artikel geeft een overzicht van live streaming met Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e2c4e5b6c10b06d82a1933962cb2d97e031876a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068027"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Live streamen met Azure Media Services v3

Met Azure Media Services u live-evenementen leveren aan uw klanten in de Azure-cloud. Als je je live-evenementen wilt streamen met Media Services, heb je het volgende nodig:  

- Een camera die wordt gebruikt om het live-evenement vast te leggen.<br/>Voor setup ideeën, check out [Eenvoudige en draagbare gebeurtenis video gear setup]( https://link.medium.com/KNTtiN6IeT).

    Als u geen toegang hebt tot een camera, kunnen hulpmiddelen zoals [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) worden gebruikt om een live feed uit een videobestand te genereren.
- Een live video-encoder die signalen van een camera (of een ander apparaat, zoals een laptop) omzet in een bijdragefeed die naar Media Services wordt verzonden. De bijdragefeed kan signalen bevatten met betrekking tot reclame, zoals SCTE-35-markeringen.<br/>Zie live streaming encoders voor een lijst met aanbevolen live streaming [encoders.](recommended-on-premises-live-encoders.md) Bekijk ook deze blog: [Live streaming productie met OBS](https://link.medium.com/ttuwHpaJeT).
- Componenten in Media Services, waarmee u het live-evenement opnemen, bekijken, verpakken, versleutelen en uitzenden naar uw klanten, of naar een CDN voor verdere distributie.

Dit artikel geeft een overzicht en begeleiding van live streaming met Media Services en links naar andere relevante artikelen.
 
> [!NOTE]
> U de [Azure-portal](https://portal.azure.com/) gebruiken om v3 [Live Events](live-events-outputs-concept.md), bekijk [v3-assets](assets-concept.md), informatie te krijgen over toegang tot API's. Gebruik voor alle andere beheertaken (bijvoorbeeld Transformaties en Taken) de [REST API,](https://docs.microsoft.com/rest/api/media/) [CLI](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [SDK's.](media-services-apis-overview.md#sdks)

## <a name="dynamic-packaging"></a>Dynamische pakketten

Met Media Services u profiteren van [Dynamic Packaging](dynamic-packaging-overview.md), waarmee u uw livestreams bekijken en uitzenden in [MPEG DASH-, HLS- en Smooth Streaming-indelingen](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) via de bijdragefeed die naar de service wordt verzonden. Je kijkers kunnen de live stream afspelen met alle HLS-, DASH- of Smooth Streaming-compatibele spelers. U [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) in uw web- of mobiele toepassingen gebruiken om uw stream in een van deze protocollen te leveren.

## <a name="dynamic-encryption"></a>Dynamische versleuteling

Dynamische versleuteling stelt u in staat om uw live of on-demand content dynamisch te versleutelen met AES-128 of een van de drie grote DRM-systemen (Digital Rights Management): Microsoft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties (PlayReady, Widevine en FairPlay) aan geautoriseerde clients. Zie [Dynamische versleuteling](content-protection-overview.md)voor meer informatie.

> [!NOTE]
> Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="dynamic-manifest"></a>Dynamisch manifest

Dynamische filtering wordt gebruikt om het aantal tracks, formaten, bitrates en presentatietijdvensters te regelen die naar de spelers worden verzonden. Zie [filters en dynamische manifesten](filters-dynamic-manifest-overview.md)voor meer informatie.

## <a name="live-event-types"></a>Typen live gebeurtenissen

[Livegebeurtenissen](https://docs.microsoft.com/rest/api/media/liveevents) zijn verantwoordelijk voor het opnemen en verwerken van de live videofeeds. Een live-evenement kan worden ingesteld op een *pass-through* (een on-premises live encoder stuurt een meervoudige bitrate stream) of *live codering* (een on-premises live encoder stuurt een enkele bitrate stream). Zie [Live-evenementen en Live-uitgangen](live-events-outputs-concept.md)voor meer informatie over live streaming in Media Services v3.

### <a name="pass-through"></a>Pass-through

![passthrough](./media/live-streaming/pass-through.svg)

Wanneer u het pass-through **Live Event gebruikt,** vertrouwt u op uw on-premises live encoder om een videostream met meerdere bitrates te genereren en dat te verzenden als de bijdragefeed naar het Live Event (met RTMP of gefragmenteerd-MP4-invoerprotocol). Het Live Event voert vervolgens door de inkomende videostreams naar de dynamische verpakker (Streaming Endpoint) zonder verdere transcodering. Zo'n pass-through Live Event is geoptimaliseerd voor langlopende live events of 24x365 lineaire live streaming. 

### <a name="live-encoding"></a>Live Encoding  

![live encoding](./media/live-streaming/live-encoding.svg)

Wanneer u cloudcodering met Media Services gebruikt, configureert u uw on-premises live-encoder om één bitrate-video te verzenden als de bijdragefeed (tot een aggregaat van 32 Mbps) naar de Live-gebeurtenis (met RTMP of gefragmenteerd-MP4-invoerprotocol). Het Live Event transcodeert de binnenkomende single bitrate stream in [meerdere bitrate videostreams](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) met verschillende resoluties om de levering te verbeteren en maakt het beschikbaar voor levering aan afspeelapparaten via industriestandaard protocollen zoals MPEG-DASH, Apple HTTP Live Streaming (HLS) en Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Live transcriptie (preview)

Live transcriptie is een functie die u gebruiken met live-evenementen die doorgaan of live coderen. Zie [live transcriptie](live-transcription.md)voor meer informatie. Wanneer deze functie is ingeschakeld, gebruikt de service de [spraak-naar-tekst-functie](../../cognitive-services/speech-service/speech-to-text.md) van Cognitive Services om de gesproken woorden in de binnenkomende audio in tekst te transcriberen. Deze tekst wordt vervolgens beschikbaar gesteld voor levering, samen met video en audio in MPEG-DASH en HLS protocollen.

> [!NOTE]
> Momenteel is live transcriptie beschikbaar als preview-functie in West US 2.

## <a name="live-streaming-workflow"></a>Werkstroom voor live streaming

Om de live streaming workflow in Media Services v3 te begrijpen, moet u eerst de volgende concepten bekijken en begrijpen: 

- [Streaming eindpunten](streaming-endpoint-concept.md)
- [Livegebeurtenissen en live-uitvoer](live-events-outputs-concept.md)
- [Streaming-locators](streaming-locators-concept.md)

### <a name="general-steps"></a>Algemene stappen

1. Controleer in uw Media Services-account of het **streamingeindpunt** (Origin) wordt uitgevoerd. 
2. Maak een [live-evenement](live-events-outputs-concept.md). <br/>Wanneer u de gebeurtenis maakt, u opgeven om deze automatisch te starten. U het evenement ook starten wanneer u klaar bent om te beginnen met streamen.<br/> Wanneer autostart is ingesteld op true, wordt het Live Event direct na de creatie gestart. De facturering begint zodra het live-evenement wordt gestart. U moet expliciet Stop aanroepen in de resource van de livegebeurtenis om verdere facturering stop te zetten. Zie [Live Event states and billing](live-event-states-billing.md) (Statussen en facturering voor livegebeurtenissen) voor meer informatie.
3. Download de inname URL(s) en configureer uw on-premises encoder om de URL te gebruiken om de bijdragefeed te verzenden.<br/>Zie [aanbevolen live encoders](recommended-on-premises-live-encoders.md).
4. Download de preview-URL en gebruik deze om te controleren of de invoer van de encoder daadwerkelijk wordt ontvangen.
5. Een nieuw **object Asset** maken. 

    Elke Live-uitvoer is gekoppeld aan een asset, waarmee de video wordt opgenomen in de bijbehorende Azure blob-opslagcontainer. 
6. Maak een **live-uitvoer** en gebruik de assetnaam die u hebt gemaakt, zodat de stream kan worden gearchiveerd in het item.

    Live-uitvoer starten zodra ze zijn gemaakt en stoppen wanneer ze worden verwijderd. Wanneer u de live-uitvoer verwijdert, verwijdert u de onderliggende waarde en inhoud in het item niet.
7. Maak een **streaminglocator** met de [ingebouwde typen streamingbeleid.](streaming-policy-concept.md)

    Als u de Live Output wilt publiceren, moet u een streaminglocator maken voor het bijbehorende item. 
8. Vermeld de paden op de **Streaming Locator** om de URL's terug te krijgen die u wilt gebruiken (deze zijn deterministisch).
9. Download de hostnaam voor het **Streaming Endpoint** (Origin) waarvan je wilt streamen.
10. Combineer de URL van stap 8 met de hostnaam in stap 9 om de volledige URL te krijgen.
11. Als je wilt stoppen met het zichtbaar maken van je **Live Event,** moet je stoppen met het streamen van het evenement en de **Streaming Locator**verwijderen.
12. Als u klaar bent met het streamen van gebeurtenissen en de resources wilt opruimen die eerder zijn ingericht, volgt u de volgende procedure.

    * Stop het pushen van de stream vanuit het coderingsprogramma.
    * Stop de livegebeurtenis. Zodra de livegebeurtenis is gestopt, worden hiervoor geen kosten meer in rekening gebracht. Als u het kanaal opnieuw wilt starten, wordt dezelfde URL voor opnemen gebruikt, zodat u het coderingsprogramma niet opnieuw hoeft te configureren.
    * U kunt uw streaming-eindpunt stoppen, tenzij u het archief van uw live gebeurtenis wilt blijven leveren als stream op aanvraag. Voor een livegebeurtenis die is gestopt, worden er geen kosten meer in rekening gebracht.

Het item waarop de live-uitvoer wordt gearchiveerd, wordt automatisch een on-demand asset wanneer de live-uitvoer wordt verwijderd. U moet alle live-uitgangen verwijderen voordat een live-gebeurtenis kan worden gestopt. U een optionele vlag [verwijderenUitvoergebruikengebruikenom](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) automatisch live-uitgangen bij de stop te verwijderen. 

> [!TIP]
> Zie [Live streaming tutorial](stream-live-tutorial-with-api.md), het artikel onderzoekt de code die de hierboven beschreven stappen implementeert.

## <a name="other-important-articles"></a>Andere belangrijke artikelen

- [Aanbevolen live-encoders](recommended-on-premises-live-encoders.md)
- [Een cloud-DVR gebruiken](live-event-cloud-dvr.md)
- [Vergelijking van typen live-gebeurtenissen](live-event-types-comparison.md)
- [Statussen en facturering](live-event-states-billing.md)
- [Latentie](live-event-latency.md)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

Zie het [artikel over veelgestelde vragen.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

* [Live streaming quickstart] (live-events-wirecast-quickstart.md.
* [Zelfstudie voor live streaming](stream-live-tutorial-with-api.md)
* [Migratierichtlijnen voor de overgang van Media Services v2 naar v3](migrate-from-v2-to-v3.md)
