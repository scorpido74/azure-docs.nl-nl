---
title: LiveEvent-instellingen voor lage latentie in Azure Media Services | Microsoft Documenten
description: In dit onderwerp vindt u een overzicht van live-instellingen met lage latentie en wordt uitgelegd hoe u lage latentie instellen.
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
ms.openlocfilehash: a82a0644fac099b568ab86ea213b98cd8e7d5c22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199645"
---
# <a name="live-event-low-latency-settings"></a>Instellingen voor lage latentie van live-gebeurtenis

In dit artikel ziet u hoe u lage latentie instelt op een [live-evenement](https://docs.microsoft.com/rest/api/media/liveevents). Het bespreekt ook typische resultaten die u ziet bij het gebruik van de lage latentie-instellingen in verschillende spelers. De resultaten variëren op basis van CDN en netwerklatentie.

Als u de nieuwe **functie LowLatency** wilt gebruiken, stelt u de **StreamOptionsFlag** in op **LowLatency** op het **LiveEvent.** Wanneer u [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) maakt voor het afspelen van HLS, stelt u [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) in op 1. Zodra de stream actief is, u de [Azure Media Player](https://ampdemo.azureedge.net/) (AMP-demopagina) gebruiken en de afspeelopties instellen om het profiel 'Low Latency Heuristics' te gebruiken.

> [!NOTE]
> Momenteel is de LowLatency HeuristicProfile in Azure Media Player ontworpen voor het afspelen van streams in het `format=mdp-time-csf` MPEG-DASH-protocol, met een CSF- of CMAF-indeling (bijvoorbeeld of `format=mdp-time-cmaf`). 

In het volgende .NET-voorbeeld ziet u hoe **u lowlatency** instelt op het **LiveEvent:**

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

Zie het volledige voorbeeld: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Latentie voor livegebeurtenissen

In de volgende tabellen worden typische resultaten weergegeven voor latentie (wanneer de vlag LowLatency is ingeschakeld) in Media Services, gemeten vanaf het moment dat de bijdragefeed de service bereikt tot wanneer een kijker het afspelen op de speler ziet. Als u lage latentie optimaal wilt gebruiken, moet u uw encoderinstellingen afstemmen op de lengte van 1 seconde "Groep van foto's". Wanneer u een hogere GOP-lengte gebruikt, minimaliseert u het bandbreedteverbruik en vermindert u de bitrate onder dezelfde framesnelheid. Het is vooral gunstig in video's met minder beweging.

### <a name="pass-through"></a>Pass-through 

||2s GOP lage latentie ingeschakeld|1s GOP lage latentie ingeschakeld|
|---|---|---|
|DASH in AMP|10s|8s|
|HLS op native iOS-speler|14s|10s|

### <a name="live-encoding"></a>Live Encoding

||2s GOP lage latentie ingeschakeld|1s GOP lage latentie ingeschakeld|
|---|---|---|
|DASH in AMP|14s|10s|
|HLS op native iOS-speler|18s|13s|

> [!NOTE]
> De end-to-end latentie kan variëren afhankelijk van de lokale netwerkomstandigheden of door de invoering van een CDN-cachinglaag. U moet uw exacte configuraties testen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van live streaming](live-streaming-overview.md)
- [Zelfstudie voor live streaming](stream-live-tutorial-with-api.md)

