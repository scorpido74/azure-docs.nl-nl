---
title: Clouds en regio's waarin Azure Media Services v3 beschikbaar is
description: Dit artikel spreekt over Azure-Clouds en-regio's waarin Azure Media Services v3 beschikbaar is.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 9c54482dc188141c4fc697bd43628b81e0ddc29f
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597130"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Clouds en regio's waarin Azure Media Services v3 bestaat

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3 is beschikbaar via Azure Resource Manager-manifest in Global Azure, Azure Government, Azure Duitsland, Azure China 21Vianet. Niet alle Media Services-functies zijn echter beschikbaar in alle Azure-Clouds. In dit document wordt een overzicht van de beschik baarheid van belangrijkste Media Services v3-onderdelen beschreven.

## <a name="feature-availability-in-azure-clouds"></a>Beschik baarheid van functies in azure-Clouds

| Functie|Wereld wijde Azure-regio's | Azure Government|Azure Duitsland|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| [StandardEncoderPreset](encoding-concept.md) | Beschikbaar | Beschikbaar | Beschikbaar | Beschikbaar |
| [LiveEvents](live-streaming-overview.md) | Beschikbaar | Beschikbaar | Beschikbaar | Beschikbaar |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Beschikbaar | Beschikbaar | Beschikbaar | Beschikbaar |

## <a name="feature-availability-in-preview"></a>Beschik baarheid van functies in Preview

[LiveTranscription](live-transcription.md) is beschikbaar in de volgende regio's:

- Azië - zuidoost
- Europa -west
- Europa - noord
- VS - oost
- Central US
- VS - zuid-centraal
- VS - west 2
- Brazil South

## <a name="regionsgeographieslocations"></a>Regio's/geografi/locaties

[Regio's waarin de Azure Media Services-service is geïmplementeerd](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Regio code naam

Wanneer u de **locatie** parameter moet opgeven, moet u de naam van de regio code opgeven als **locatie** waarde. Als u de code naam wilt ophalen van de regio waarin uw account zich bevindt en de aanroep moet worden doorgestuurd naar, kunt u de volgende regel uitvoeren in [Azure cli](/cli/azure/?view=azure-cli-latest)

```azurecli-interactive
az account list-locations
```

Wanneer u de hierboven weer gegeven regel hebt uitgevoerd, krijgt u een lijst met alle Azure-regio's. Ga naar de Azure-regio met de *DisplayName* die u zoekt en gebruik de *naam* waarde voor de **locatie** parameter.

Voor de Azure-regio vs-West 2 (hieronder weer gegeven) gebruikt u bijvoorbeeld ' westus2 ' voor de **locatie** parameter.

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

De volgende eind punten zijn belang rijk om te weten wanneer u verbinding maakt met Media Services accounts van verschillende nationale Azure-Clouds.

### <a name="global-azure"></a>Wereld wijd Azure

| Service | Eindpunt |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Verificatie | `https://login.microsoftonline.com/` |
| Token doelgroep | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Service | Eindpunt |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Verificatie | `https://login.microsoftonline.us/` |
| Token doelgroep | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Duitsland

| Service | Eindpunt |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Verificatie | `https://login.microsoftonline.de/` |
| Token doelgroep | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

| Service | Eindpunt |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Verificatie | `https://login.chinacloudapi.cn/` |
| Token doelgroep |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Zie ook

* [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/)
* [Geografische gebieden in Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Media Services v3](media-services-overview.md)
