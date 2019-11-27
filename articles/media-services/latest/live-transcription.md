---
title: Azure Media Services Live transcriptie | Microsoft Docs
description: In dit artikel wordt uitgelegd wat de Azure Media Services Live transcriptie is.
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
ms.openlocfilehash: a85f9f8b9d98f77cf673778f031d8f47f132fbe1
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327349"
---
# <a name="live-transcription-preview"></a>Live transcriptie (preview-versie)

De Azure media-service levert video, audio en nu tekst in verschillende protocollen. Wanneer u uw Live Stream publiceert met MPEG-DASH of HLS/CMAF en vervolgens video en audio, levert onze service de getranscribeerde tekst in IMSC 1.1 compatibele TTML, verpakt in fragmenten van MPEG-4 Part 30 (ISO/IEC 14496-30). Als u levering via HLS/TS gebruikt, wordt de tekst geleverd als gesegmenteerde VTT. 

In dit artikel wordt beschreven hoe u live transcriptie inschakelt bij het streamen van een live-gebeurtenis met Azure Media Services v3. Voordat u doorgaat, moet u ervoor zorgen dat u bekend bent met het gebruik van Media Services v3 REST-Api's (Zie [deze zelf studie](stream-files-tutorial-with-rest.md) voor meer informatie). U moet ook vertrouwd zijn met het concept van [live streamen](live-streaming-overview.md) . U wordt aangeraden de [stroom Live met Media Services](stream-live-tutorial-with-api.md) zelf studie te volt ooien. 

> [!NOTE]
> Live transcriptie is momenteel alleen beschikbaar als preview-functie in de regio vs-West 2. Het ondersteunt transcriptie van gesp roken woorden in het Engels naar tekst. De API-verwijzing voor deze functie bevindt zich in dit document: aangezien deze in preview is, zijn de details niet beschikbaar in onze REST-documenten. 

## <a name="creating-the-live-event"></a>De live-gebeurtenis maken 

Als u de live-gebeurtenis wilt maken, verzendt u de PUT-bewerking naar de 2019-05-01-versie, zoals: 

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

De bewerking heeft de volgende hoofd tekst (waarbij een Pass-through live-gebeurtenis wordt gemaakt met RTMP als het opname Protocol). Let op de toevoeging van een eigenschap transcripties. De enige toegestane waarde voor de taal en-US. 

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

U moet de status van de live-gebeurtenis controleren totdat deze in de status ' running ' komt, wat aangeeft dat u nu een contributie-RTMP-feed kunt verzenden. U kunt nu dezelfde stappen volgen als in deze zelf studie, zoals het controleren van de preview-feed en het maken van live uitvoer. 

## <a name="delivery-and-playback"></a>Levering en afspelen 

Raadpleeg het [overzicht van dynamische](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) pakketten voor informatie over hoe de service dynamische pakketten gebruikt voor het leveren van video, audio en nu tekst in verschillende protocollen. Wanneer u uw Live Stream publiceert met MPEG-DASH of HLS/CMAF en vervolgens video en audio, levert onze service de getranscribeerde tekst in IMSC 1.1 compatibele TTML, verpakt in fragmenten van MPEG-4 Part 30 (ISO/IEC 14496-30). Als u levering via HLS/TS gebruikt, wordt de tekst geleverd als gesegmenteerde VTT. U kunt een webspeler gebruiken zoals de [Azure Media Player](use-azure-media-player.md) om de stream af te spelen.  

> [!NOTE]
>  Als u Azure Media Player gebruikt, moet u versie 2.3.3 of hoger gebruiken.

## <a name="known-issues"></a>Bekende problemen 

Op de preview-versie volgen de bekende problemen met Live transcriptie 

* De functie is alleen beschikbaar in VS-West 2.
* Toepassingen moeten de preview-Api's gebruiken, zoals beschreven in de specificatie [Media Services v3 OpenAPI](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json) .
* De enige ondersteunde taal is Engels.
* Met betrekking tot Content Protection wordt alleen AES-envelop versleuteling ondersteund.

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Media Services](media-services-overview.md)
