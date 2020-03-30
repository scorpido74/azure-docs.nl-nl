---
title: Clouds en regio's waarin Azure Media Services v3 beschikbaar is
description: In dit artikel wordt gesproken over Azure-clouds en -regio's waarin Azure Media Services v3 beschikbaar is.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 1257bf4dfb0d5b2c4995cac760290f97293a0c0f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382967"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Clouds en regio's waarin Azure Media Services v3 bestaat

Azure Media Services v3 is beschikbaar via azure resource manager-manifest in het wereldwijde Azure, Azure Government, Azure Germany, Azure China 21Vianet. Niet alle Media Services-functies zijn echter beschikbaar in alle Azure-clouds. In dit document worden de beschikbaarheid van de belangrijkste V3-componenten van Media Services beschreven.

## <a name="feature-availability-in-azure-clouds"></a>Beschikbaarheid van functies in Azure-clouds

| Functie|Globale Azure-regio's | Azure Government|Azure Duitsland|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| [StandardEncoderPreset](encoding-concept.md) | Beschikbaar | Beschikbaar | Beschikbaar | Beschikbaar |
| [LiveEvenementen](live-streaming-overview.md) | Beschikbaar | Beschikbaar | Beschikbaar | Beschikbaar |
| [StreamingEindpunten](streaming-endpoint-concept.md) | Beschikbaar | Beschikbaar | Beschikbaar | Beschikbaar |

## <a name="regionsgeographieslocations"></a>Regio's/regio's/locaties

[Regio's waarin de Azure Media Services-service wordt geïmplementeerd](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Regiocodenaam

Wanneer u de **locatieparameter** moet leveren, moet u de regiocodenaam als **locatiewaarde** opgeven. Als u de codenaam wilt krijgen van het gebied waarin uw account zich bevindt en naar uw oproep moet worden doorgestuurd, u de volgende regel uitvoeren in [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```azurecli-interactive
az account list-locations
```

Zodra u de bovenstaande regel uitvoert, krijgt u een lijst met alle Azure-regio's. Navigeer naar het Azure-gebied met de *weergaveNaam die* u zoekt en gebruik de *naamwaarde* voor de **locatieparameter.**

Voor de Azure-regio West US 2 (hieronder weergegeven) gebruikt u bijvoorbeeld 'westus2' voor de **locatieparameter.**

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Eindpunten  

De volgende eindpunten zijn belangrijk om te weten wanneer u verbinding maakt met Media Services-accounts uit verschillende nationale Azure-clouds.

### <a name="global-azure"></a>Globale Azure

|Eindpunten||
| --- | --- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` |
| Token-doelgroep | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Eindpunten||
| --- | --- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` |
| Token-doelgroep | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Duitsland

| Eindpunten ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| Token-doelgroep | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|Eindpunten||
| --- | --- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| Token-doelgroep |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Zie ook

* [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/)
* [Geografische gebieden in Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Volgende stappen

[Media Services v3 overzicht](media-services-overview.md)
