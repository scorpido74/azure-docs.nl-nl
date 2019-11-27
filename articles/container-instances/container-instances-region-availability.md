---
title: Resource beschikbaarheid per regio
description: Beschik baarheid van reken-en geheugen resources voor de Azure Container Instances-service in verschillende Azure-regio's.
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: aef66a9fdbe73ccd4da79ce972b7beb061e9fe35
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533475"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Beschik baarheid van resources voor Azure Container Instances in azure-regio's

In dit artikel vindt u informatie over de beschik baarheid van Azure Container Instances Compute-en geheugen resources in azure-regio's. 

De weer gegeven waarden zijn de Maxi maal beschik bare bronnen per implementatie van een [container groep](container-instances-container-groups.md). Waarden zijn actueel op het moment van publicatie. 

> [!NOTE]
> Container groepen die zijn gemaakt binnen deze resource limieten, zijn afhankelijk van de beschik baarheid binnen de implementatie regio. Wanneer een regio zwaar wordt belast, kan er een fout optreden bij het implementeren van instanties. Als u een dergelijke implementatie fout wilt verhelpen, implementeert u instanties met lagere bron instellingen of probeert u de implementatie op een later tijdstip.

Zie [quota's en limieten voor Azure container instances](container-instances-quotas.md)voor meer informatie over quota's en andere limieten in uw implementaties.

## <a name="availability---general"></a>Beschik baarheid-algemeen

De volgende regio's en resources zijn beschikbaar voor container groepen met Linux en [ondersteunde](container-instances-faq.md#what-windows-base-os-images-are-supported) containers op Windows Server 2016.

| Locatie | OS | CPU | Geheugen (GB) |
| -------- | -- | :---: | :-----------: |
| Canada-centraal, Centraal-India, centraal VS, Azië-oost, VS-Oost, VS-Oost 2, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, UK-zuid, VS-West | Linux | 4 | 16 |
| Europa-west, VS-West 2 | Linux | 4 | 14 |
| Australië-oost, Japan-Oost | Linux | 2 | 8 |
| VS Noord-Centraal, India-zuid | Linux | 2 | 3,5 |
| Europa -west | Windows | 4 | 16 |
| US - oost, US - west | Windows | 4 | 14 |
| Australië-oost, Canada-centraal, Centraal-India, VS-midden, Azië-oost, VS-Oost 2, Japan-Oost, Noord-Centraal VS, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, India-zuid, UK-zuid, VS-West 2 | Windows | 2 | 3,5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Beschik baarheid-Windows Server 2019 LTSC, 1809-implementaties (preview)

De volgende regio's en bronnen zijn beschikbaar voor container groepen met Windows Server 2019-containers (preview).

| Locatie | OS | CPU | Geheugen (GB) |
| -------- | -- | :---: | :-----------: |
| Zuidoost-Azië, Europa-noord, Europa-west, VS-midden, VS-Oost, VS-West, VS-West 2 | Windows | 4 | 16 |
| US - oost 2 | Windows | 2 | 3,5 |


## <a name="availability---virtual-network-deployment-preview"></a>Beschik baarheid-implementatie van virtueel netwerk (preview-versie)

De volgende regio's en resources zijn beschikbaar voor een container groep die is geïmplementeerd in een [virtueel Azure-netwerk](container-instances-vnet.md) (preview).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Beschik baarheid-GPU-resources (preview-versie)

De volgende regio's en resources zijn beschikbaar voor een container groep die is geïmplementeerd met [GPU-bronnen](container-instances-gpu.md) (preview-versie).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Volgende stappen

Laat het team weten of u extra regio's of meer Beschik baarheid van resources wilt zien op [aka.MS/ACI/feedback](https://aka.ms/aci/feedback).

Zie [problemen met implementaties oplossen met Azure container instances](container-instances-troubleshooting.md)voor meer informatie over het oplossen van problemen met de implementatie van container instanties.
