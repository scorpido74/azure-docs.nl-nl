---
title: Instellingen voor LiveEvent lage latentie in Azure Media Services | Microsoft Docs
description: Dit onderwerp bevat een overzicht van de instellingen voor LiveEvent-lage latentie en laat zien hoe u een lage latentie kunt instellen.
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
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: 24eb5f8691c94c1f319820f3133b66065504ace3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87000888"
---
# <a name="live-event-low-latency-settings"></a>Instellingen voor lage latentie van Live Event

In dit artikel wordt beschreven hoe u een lage latentie kunt instellen voor een [live gebeurtenis](/rest/api/media/liveevents). Ook worden de gebruikelijke resultaten besproken die u ziet wanneer u de instellingen voor lage latentie gebruikt in verschillende spelers. De resultaten variëren op basis van CDN en netwerk latentie.

Als u de nieuwe functie **LowLatency** wilt gebruiken, stelt u de **StreamOptionsFlag** in op **LowLatency** op de **LiveEvent**. Wanneer u [LiveOutput](/rest/api/media/liveoutputs) maakt voor HLS afspelen, stelt u [LiveOutput. HLS. fragmentsPerTsSegment](/rest/api/media/liveoutputs/create#hls) in op 1. Zodra de stroom actief is, kunt u de [Azure Media Player](https://ampdemo.azureedge.net/) (pagina amp-demo) gebruiken en de afspeel opties instellen op het gebruik van het profiel ' laag latentie heuristiek '.

> [!NOTE]
> Op dit moment is de LowLatency HeuristicProfile in Azure Media Player ontworpen voor het afspelen van streams in MPEG-DASH protocol, met een KVP-of CMAF-indeling (bijvoorbeeld `format=mdp-time-csf` of `format=mdp-time-cmaf` ). 

In het volgende .NET-voor beeld ziet u hoe u **LowLatency** instelt op de **LiveEvent**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Bekijk het volledige voor beeld: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Latentie van Live-gebeurtenissen

In de volgende tabellen worden de gemiddelde resultaten weer gegeven voor latentie (wanneer de vlag LowLatency is ingeschakeld) in Media Services, gemeten vanaf het moment dat de bijdrage toevoer de service bereikt wanneer een viewer het afspelen in de speler ziet. Als u de maximale latentie optimaal wilt gebruiken, moet u de instellingen voor de code ring afstemmen op 1 seconde ' groep afbeeldingen ' (GOP terug). Wanneer u een hogere GOP terug lengte gebruikt, minimaliseert u het bandbreedte verbruik en vermindert u de bitsnelheid onder dezelfde frame frequentie. Het is vooral nuttig in Video's met minder beweging.

### <a name="pass-through"></a>Pass-through 

||2-GOP terug lage latentie ingeschakeld|1S GOP terug lage latentie ingeschakeld|
|---|---|---|
|**STREEPJE in AMP**|10|8s|
|**HLS op systeem eigen iOS-speler**|14s|10|

### <a name="live-encoding"></a>Live Encoding

||2-GOP terug lage latentie ingeschakeld|1S GOP terug lage latentie ingeschakeld|
|---|---|---|
|**STREEPJE in AMP**|14s|10|
|**HLS op systeem eigen iOS-speler**|18s|13s|

> [!NOTE]
> De end-to-end-latentie kan variëren afhankelijk van de lokale netwerk omstandigheden of door een CDN-cache laag te introduceren. U moet uw exacte configuraties testen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van live streamen](live-streaming-overview.md)
- [Zelf studie over live streamen](stream-live-tutorial-with-api.md)
