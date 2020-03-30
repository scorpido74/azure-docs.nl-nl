---
title: LiveEvent-typen Azure Media Services | Microsoft Documenten
description: In Azure Media Services kan een *live-gebeurtenis* worden ingesteld op een doorreis of *live codering.* In dit artikel wordt een gedetailleerde tabel weergegeven waarin typen live gebeurtenissen worden vergeleken.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: a28d4d96f643c12eeb6aa542db2c6af06f4fd954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244645"
---
# <a name="live-event-types-comparison"></a>Vergelijking van typen live-gebeurtenissen

In Azure Media Services kan een [Live-gebeurtenis](https://docs.microsoft.com/rest/api/media/liveevents) worden ingesteld op een *doorgeeftoepassing* (een on-premises live-encoder verzendt een stream met meerdere bitrate' s of *live codering* (een on-premises live-encoder verzendt één bitratestream). 

In deze artikelen worden functies van de typen live-evenementen vergeleken.

## <a name="types-comparison"></a>Typen vergelijking 

In de volgende tabel worden de functies van de typen Live-evenementen vergeleken. De typen worden ingesteld tijdens het maken met [LiveEventEncodingType:](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)

* **LiveEventEncodingType.None** - Een on-premises live encoder stuurt een meervoudige bitrate stream. De ingenomen streams gaan door het Live Event zonder verdere verwerking. Ook wel aangeduid als een pass-through Live Event.
* **LiveEventEncodingType.Standard** - Een on-premises live encoder stuurt één bitrate stream naar de Live Event en Media Services maakt meerdere bitrate streams. Als de bijdragefeed een resolutie van 720p of hoger heeft, codeert de **standaard720p-voorinstelling** een set van 6 resolutie/bitrate-paren (details volgen later in het artikel).
* **LiveEventEncodingType.Premium1080p** - Een on-premises live-encoder stuurt één bitratestream naar het Live Event en Media Services maakt meerdere bitratestreams. De standaardinstelling 1080p geeft de uitvoerset van resolutie/bitrateparen op (details volgen later in het artikel). 

| Functie | Pass-through Live Event | Standaard- of Premium1080p Live-evenement |
| --- | --- | --- |
| Enkele bitrate-invoer wordt gecodeerd in meerdere bitrates in de cloud |Nee |Ja |
| Maximale videoresolutie voor bijdragefeed |4K (4096x2160 bij 60 frames/sec) |1080p (1920x1088 bij 30 frames/sec)|
| Aanbevolen maximale lagen in bijdragefeed|Maximaal 12|Eén audio|
| Maximale lagen in uitvoer| Hetzelfde als invoer|Maximaal 6 (zie Systeemvoorinstellingen hieronder)|
| Maximale totale bandbreedte van bijdragefeed|60 Mbps|N.v.t.|
| Maximale bitrate voor één laag in de bijdrage |20 Mbps|20 Mbps|
| Ondersteuning voor audiotracks in meerdere talen|Ja|Nee|
| Ondersteunde invoervideocodecs |H.264/AVC en H.265/HEVC|H.264/AVC|
| Ondersteunde uitvoervideocodecs|Hetzelfde als invoer|H.264/AVC|
| Ondersteunde diepte, invoer en uitvoer van ondersteunde videobit|Tot 10-bits inclusief HDR 10/HLG|8-bits|
| Ondersteunde invoeraudiocodecs|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Ondersteunde uitvoeraudiocodecs|Hetzelfde als invoer|AAC-LC|
| Maximale videoresolutie van uitvoervideo|Hetzelfde als invoer|Standaard - 720p, Premium1080p - 1080p|
| Maximale framesnelheid van invoervideo|60 frames per seconde|Standaard of Premium1080p - 30 frames per seconde|
| Invoerprotocollen|RTMP, gefragmenteerd-MP4 (Smooth Streaming)|RTMP, gefragmenteerd-MP4 (Smooth Streaming)|
| Prijs|Bekijk de [prijspagina](https://azure.microsoft.com/pricing/details/media-services/) en klik op het tabblad 'Live video'|Bekijk de [prijspagina](https://azure.microsoft.com/pricing/details/media-services/) en klik op het tabblad 'Live video'|
| Maximale looptijd| 24 uur x 365 dagen, live lineair | 24 uur x 365 dagen, live lineair (preview)|
| Mogelijkheid om door ingesloten CEA 608/708-bijschriften gegevens te gaan|Ja|Ja|
| Mogelijkheid om Live Transcriptie in te schakelen|Ja|Ja|
| Ondersteuning voor het invoegen van leien|Nee|Nee|
| Ondersteuning voor het signaleren van advertenties via API| Nee|Nee|
| Ondersteuning voor advertentiesignalering via SCTE-35 in-band berichten|Ja|Ja|
| Mogelijkheid om te herstellen van korte kraampjes in bijdrage feed|Ja|Gedeeltelijk|
| Ondersteuning voor niet-uniforme input GOP's|Ja|Nee – invoer moet een vaste GOP-duur hebben|
| Ondersteuning voor invoer van variabele framesnelheid|Ja|Nee – invoer moet een vaste framesnelheid zijn. Kleine variaties worden getolereerd, bijvoorbeeld tijdens scènes met hoge beweging. Maar de bijdragefeed kan de framesnelheid niet laten zakken (bijvoorbeeld tot 15 frames per seconde).|
| Automatisch afsluiten van Live Event wanneer de invoerfeed verloren gaat|Nee|Na 12 uur, als er geen LiveOutput wordt uitgevoerd|

## <a name="system-presets"></a>Systeemvoorinstellingen

De resoluties en bitrates in de uitvoer van de live encoder worden bepaald door de [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Als u een **standaard** live-encoder gebruikt (LiveEventEncodingType.Standard), geeft de standaardinstelling *720p* een set van 6-resolutie/bitrateparen op die hieronder worden beschreven. Als u anders een **Premium1080p** live-encoder gebruikt (LiveEventEncodingType.Premium1080p), geeft de voorinstelling *Standaard1080p* de uitvoerset van resolutie/bitrateparen op.

> [!NOTE]
> U de standaardvoorinstelling 1080p niet toepassen op een live-gebeurtenis als deze is ingesteld voor standaard live codering - u krijgt een foutmelding. Je krijgt ook een foutmelding als je de Standaard720p-voorinstelling probeert toe te passen op een Premium1080p live-encoder.

### <a name="output-video-streams-for-default720p"></a>Videostreams uitvoervoor Default720p

Als de bijdragefeed een resolutie van 720p of hoger heeft, codeert de voorinstelling **Default720p** de feed in de volgende 6 lagen. In de onderstaande tabel is Bitrate in kbps, MaxFPS vertegenwoordigt die maximaal toegestane framesnelheid (in frames/seconde), Profiel vertegenwoordigt het gebruikte H.264-profiel.

| Bitrate | Breedte | Height | MaxFPS (MaxFPS) | Profiel |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Hoog |
| 2200 |960 |540 |30 |Hoog |
| 1350 |704 |396 |30 |Hoog |
| 850 |512 |288 |30 |Hoog |
| 550 |384 |216 |30 |Hoog |
| 200 |340 |192 |30 |Hoog |

> [!NOTE]
> Als u de voorinstelling voor live codering wilt aanpassen, opent u een ondersteuningsticket via Azure Portal. Geef de gewenste tabel met resoluties en bitrates op. Controleer of er slechts één laag is op 720 p, en maximaal zes lagen. Geef ook aan dat u een voorinstelling aanvraagt voor een standaard live encoder.
> De specifieke waarden van de bitrates en resoluties kunnen in de loop van de tijd worden aangepast

### <a name="output-video-streams-for-default1080p"></a>Videostreams uitvoervoor Standaard1080p

Als de bijdragefeed een resolutie van 1080p heeft, codeert de voorinstelling **Standaard1080p** de feed in de volgende 6 lagen.

| Bitrate | Breedte | Height | MaxFPS (MaxFPS) | Profiel |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Hoog |
| 3000 |1280 |720 |30 |Hoog |
| 1600 |960 |540 |30 |Hoog |
| 800 |640 |360 |30 |Hoog |
| 400 |480 |270 |30 |Hoog |
| 200 |320 |180 |30 |Hoog |

> [!NOTE]
> Als u de voorinstelling voor live codering wilt aanpassen, opent u een ondersteuningsticket via Azure Portal. Geef de gewenste tabel met resoluties en bitrates op. Controleer of er slechts één laag op 1080p is en hooguit 6 lagen. Geef ook aan dat u een voorinstelling aanvraagt voor een Premium1080p live encoder.
> De specifieke waarden van de bitrates en resoluties kunnen in de loop van de tijd worden aangepast.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Audiostream uitvoervoor Default720p en Default1080p

Voor zowel *Default720p-* als *Default1080p-voorinstellingen* wordt audio gecodeerd naar stereo-AAC-LC bij 128 kbps. De sampling rate volgt op die van het audiospoor in de bijdragefeed.

## <a name="implicit-properties-of-the-live-encoder"></a>Impliciete eigenschappen van de levende encoder

De vorige sectie beschrijft de eigenschappen van de levende encoder die expliciet kan worden gecontroleerd, via de voorinstelling - zoals het aantal lagen, resoluties en bitrates. Deze sectie verduidelijkt de impliciete eigenschappen.

### <a name="group-of-pictures-gop-duration"></a>Duur van de groep foto's (GOP)

De levende encoder volgt de [GOP-structuur](https://en.wikipedia.org/wiki/Group_of_pictures) van de bijdragefeed - wat betekent dat de uitvoerlagen dezelfde GOP-duur hebben. Daarom wordt aanbevolen dat u de on-premises encoder configureert om een bijdragefeed te produceren met een vaste GOP-duur (meestal 2 seconden). Dit zal ervoor zorgen dat de uitgaande HLS en MPEG DASH streams van de dienst ook vaste GOP-duur heeft. Kleine variaties in GOP-duur worden waarschijnlijk door de meeste apparaten getolereerd.

### <a name="frame-rate"></a>Framesnelheid

De levende encoder volgt ook de duur van de afzonderlijke videoframes in de bijdragefeed - wat betekent dat de uitvoerlagen frames met dezelfde duur hebben. Daarom wordt aanbevolen dat u de on-premises encoder configureert om een bijdragefeed te produceren met een vaste framesnelheid (maximaal 30 frames per seconde). Dit zal ervoor zorgen dat de uitgaande HLS en MPEG DASH streams van de dienst heeft ook vaste framerates duur. Kleine variaties in framerates kunnen door de meeste apparaten worden getolereerd, maar er is geen garantie dat de levende encoder een uitvoer zal produceren die correct zal spelen. Uw on-premises levende encoder mag geen frames laten vallen (bijv. onder lage batterijomstandigheden) of het variëren van de framerate op enigerlei wijze.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Oplossing van de uitvoer- en uitvoerlagen van de bijdrage

De live encoder is geconfigureerd om te voorkomen dat de bijdragefeed wordt upconverteren. Hierdoor zal de maximale resolutie van de uitvoerlagen niet hoger zijn dan die van de bijdragefeed.

Als u bijvoorbeeld een bijdragefeed op 720p verzendt naar een Live Event die is geconfigureerd voor standaard1080p live-codering, heeft de uitvoer slechts 5 lagen, te beginnen met 720p bij 3 Mbps, naar beneden naar 1080p bij 200 kbps. Of als u een bijdragefeed op 360p verzendt naar een Live Event die is geconfigureerd voor standaard live codering, bevat de uitvoer 3 lagen (bij resoluties van 288p, 216p en 192p). In het gedegenereerde geval, als u een bijdrage feed van, laten we zeggen, 160x90 pixels naar een standaard live encoder, de output zal bevatten een laag op 160x90 resolutie op dezelfde bitrate als die van de bijdrage feed.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Bitrate van de voer- en uitvoerlagen van de bijdrage

De live encoder is geconfigureerd om de bitrate-instellingen in de voorinstelling te eren, ongeacht de bitrate van de bijdragefeed. Hierdoor kan de bitrate van de uitvoerlagen groter zijn dan die van de bijdragevoeding. Als u bijvoorbeeld een bijdragefeed instuurt met een resolutie van 720p bij 1 Mbps, blijven de uitvoerlagen hetzelfde als in de bovenstaande [tabel.](live-event-types-comparison.md#output-video-streams-for-default720p)

## <a name="next-steps"></a>Volgende stappen

[Overzicht van live streaming](live-streaming-overview.md)
