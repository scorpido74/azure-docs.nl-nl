---
title: Streamingbeleid in Azure Media Services | Microsoft Documenten
description: In dit artikel wordt uitgelegd wat streamingbeleid is en hoe deze worden gebruikt door Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: a813c77e81e51bfe13e75ed6c8d0e24b4d0fa645
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66392925"
---
# <a name="streaming-policies"></a>Beleid voor streaming

Met [Streamingbeleid](https://docs.microsoft.com/rest/api/media/streamingpolicies) u in Azure Media Services v3 streamingprotocollen en versleutelingsopties definiëren voor uw [streaminglocators.](streaming-locators-concept.md) Media Services v3 biedt een aantal vooraf gedefinieerde streamingbeleidsregels, zodat u ze rechtstreeks gebruiken voor proefversie of productie. 

Het momenteel beschikbare vooraf gedefinieerde streamingbeleid:<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

Met de volgende 'beslissingsstructuur' u een vooraf gedefinieerd streamingbeleid voor uw scenario kiezen.

> [!IMPORTANT]
> * Eigenschappen van **streamingbeleid** die van het type Datetime zijn, zijn altijd in UTC-indeling.
> * U moet een beperkt aantal beleidsregels voor uw Media Service-account ontwerpen en deze opnieuw gebruiken voor uw streaminglocators wanneer dezelfde opties nodig zijn. Zie [Quota en beperkingen](limits-quotas-constraints.md)voor meer informatie .

## <a name="decision-tree"></a>Beslissingsstructuur

Klik op de afbeelding om deze in volledig formaat weer te geven.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Als u uw inhoud versleutelt, moet u een [inhoudssleutelbeleid](content-key-policy-concept.md)maken, het **beleid voor inhoudssleutel** is niet nodig voor het wissen van streaming of downloaden. 

Als u speciale vereisten hebt (bijvoorbeeld als u verschillende protocollen wilt opgeven, een aangepaste service voor het leveren van sleutels moet gebruiken of een duidelijke audiotrack moet gebruiken), u een aangepast streamingbeleid [maken.](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) 

## <a name="get-a-streaming-policy-definition"></a>Een definitie van streamingbeleid  

Als u de definitie van een streamingbeleid wilt zien, gebruikt u [Opget](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) en geeft u de beleidsnaam op. Bijvoorbeeld:

### <a name="rest"></a>REST

Aanvraag:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Reactie:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filteren, bestellen, paging

Zie [Het filteren, bestellen, paging van mediaservices entiteiten](entities-overview.md).

## <a name="next-steps"></a>Volgende stappen

* [Een bestand streamen](stream-files-dotnet-quickstart.md)
* [AES-128 dynamische versleuteling en de key delivery service gebruiken](protect-with-aes128.md)
* [De Digital Rights Management-service gebruiken voor dynamische versleuteling en licentielevering](protect-with-drm.md)
