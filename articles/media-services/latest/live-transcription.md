---
title: Live transcriptie
titleSuffix: Azure Media Services
description: Meer informatie over live transcriptie van Azure Media Services.
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
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499871"
---
# <a name="live-transcription-preview"></a>Live transcriptie (preview)

Azure Media Service levert video, audio en tekst in verschillende protocollen. Wanneer u uw live stream publiceert met MPEG-DASH of HLS/CMAF, levert onze service samen met video en audio de getranscribeerde tekst in IMSC1.1-compatibele TTML. De levering is verpakt in MPEG-4 Part 30 (ISO/IEC 14496-30) fragmenten. Bij gebruik levering via HLS/TS, dan wordt tekst geleverd als gechunked VTT.

In dit artikel wordt beschreven hoe u live transcriptie inschakelen bij het streamen van een Live Event met Azure Media Services v3. Voordat u verdergaat, moet u ervoor zorgen dat u bekend bent met het gebruik van Media Services v3 REST API's (zie [deze zelfstudie](stream-files-tutorial-with-rest.md) voor meer informatie). Je moet ook bekend zijn met de [live streaming](live-streaming-overview.md) concept. Het wordt aanbevolen om de live stream te voltooien met de zelfstudie [van Media Services.](stream-live-tutorial-with-api.md)

> [!NOTE]
> Momenteel is live transcriptie alleen beschikbaar als voorbeeldfunctie in de regio West US 2. Het ondersteunt transcriptie van gesproken woorden in het Engels tot tekst. De API-referentie voor deze functie bevindt zich hieronder- becasuse het is in preview, de details zijn niet beschikbaar met onze REST-documenten.

## <a name="creating-the-live-event"></a>Het live-evenement maken

Als u het Live-evenement wilt maken, stuurt u de PUT-bewerking naar de versie 2019-05-01-preview, bijvoorbeeld:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

De bewerking heeft de volgende instantie (waarbij een pass-through Live Event wordt gemaakt met RTMP als innameprotocol). Let op de toevoeging van een transcriptieeigenschap. De enige toegestane waarde voor taal is en-US.

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

Peil de status van het Live Event totdat deze in de status 'Lopend' gaat, wat aangeeft dat u nu een RTMP-feed verzenden. U nu dezelfde stappen volgen als in deze zelfstudie, zoals het controleren van de voorbeeldfeed en het maken van Live-uitvoer.

## <a name="transcription-delivery-and-playback"></a>Transcriptie levering en afspelen

Bekijk het [overzichtsartikel voor dynamische verpakkingen](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) over hoe onze service dynamische verpakkingen gebruikt om video, audio en tekst in verschillende protocollen te leveren. Wanneer u uw live stream publiceert met MPEG-DASH of HLS/CMAF, levert onze service samen met video en audio de getranscribeerde tekst in IMSC1.1-compatibele TTML. Deze levering is verpakt in MPEG-4 Part 30 (ISO/IEC 14496-30) fragmenten. Bij gebruik levering via HLS/TS, dan wordt de tekst geleverd als gechunked VTT. U een webspeler zoals de [Azure Media Player](use-azure-media-player.md) gebruiken om de stream af te spelen.  

> [!NOTE]
> Als u Azure Media Player gebruikt, gebruikt u versie 2.3.3 of hoger.

## <a name="known-issues"></a>Bekende problemen

Voor preview zijn de volgende bekende problemen met live transcriptie:

* De functie is alleen beschikbaar in West US 2.
* Apps moeten de preview-API's gebruiken, beschreven in de [Media Services v3 OpenAPI-specificatie.](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)
* De enige ondersteunde taal is Engels (en-us).
* Met inhoudsbescherming wordt alleen AES-envelopversleuteling ondersteund.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Media Services](media-services-overview.md)
