---
title: Resource beschikbaarheid per regio
description: Beschik baarheid van reken-en geheugen resources voor de Azure Container Instances-service in verschillende Azure-regio's.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 1ed3f50198c0410d9c893fe87523fa214ca03d88
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521455"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Beschik baarheid van resources voor Azure Container Instances in azure-regio's

In dit artikel vindt u informatie over de beschik baarheid van Azure Container Instances compute, geheugen en opslag resources in azure-regio's en op het doel besturingssysteem. Zie [beschik bare regio's](https://azure.microsoft.com/regions/services/)voor een algemene lijst met beschik bare regio's voor Azure container instances.

De weer gegeven waarden zijn de Maxi maal beschik bare bronnen per implementatie van een [container groep](container-instances-container-groups.md). Waarden zijn actueel op het moment van publicatie.

> [!NOTE]
> Container groepen die zijn gemaakt binnen deze resource limieten, zijn afhankelijk van de beschik baarheid binnen de implementatie regio. Wanneer een regio zwaar wordt belast, kan er een fout optreden bij het implementeren van instanties. Als u een dergelijke implementatie fout wilt verhelpen, implementeert u instanties met lagere bron instellingen of probeert u de implementatie op een later tijdstip of in een andere regio met beschik bare resources.

Zie [quota's en limieten voor Azure container instances](container-instances-quotas.md)voor meer informatie over quota's en andere limieten in uw implementaties.

## <a name="linux-container-groups"></a>Linux-container groepen

De volgende regio's en maximum resources zijn beschikbaar voor container groepen met Linux-containers in algemene implementaties, Azure-implementaties van [virtuele netwerken](container-instances-vnet.md) en implementaties met [GPU-bronnen](container-instances-gpu.md) (preview).

> [!IMPORTANT]
> De maximale bronnen in een regio verschillen afhankelijk van uw implementatie. Een regio kan bijvoorbeeld een andere maximale CPU-en geheugen grootte hebben in een implementatie van een virtueel Azure-netwerk dan voor een algemene implementatie. Dezelfde regio kan ook een andere set maximum waarden hebben voor een implementatie met GPU-resources. Controleer uw implementatie type voordat u de onderstaande tabellen controleert op de maximum waarden in uw regio.

| Regio | Maximaal CPU-gebruik | Maxi maal geheugen (GB) | Maximale CPU voor VNET | Max. VNET-geheugen (GB) | Opslag (GB) | GPU-Sku's (preview-versie) |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| Australië - oost | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| Brazil South | 4 | 16 | 2 | 8 | 50 | N.v.t. |
| Canada - midden | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| India - centraal | 4 | 16 | N.v.t. | N.v.t. | 50 | V100 |
| Central US | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| Azië - oost | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| VS - oost | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| US - oost 2 | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| Japan - oost | 2 | 8 | 4 | 16 | 50 | N.v.t. |
| Korea - centraal | 4 | 16 | N.v.t. | N.v.t. | 50 | N.v.t. |
| VS - noord-centraal | 2 | 3,5 | 4 | 16 | 50 | N.v.t. |
| Europa - noord | 4 | 16 | 4 | 16 | 50 | K80 |
| South Central US | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| Azië - zuidoost | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| India - zuid | 4 | 16 | N.v.t. | N.v.t. | 50 | N.v.t. |
| Verenigd Koninkrijk Zuid | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| VS - west-centraal| 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| Europa -west | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| VS - west | 4 | 16 | 2 | 4 | 16| N.v.t. |
| West US 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |

De volgende maximum bronnen zijn beschikbaar voor een container groep die is geïmplementeerd met [GPU-bronnen](container-instances-gpu.md) (preview-versie).

| GPU-Sku's | Aantal GPU | Maximaal CPU-gebruik | Maxi maal geheugen (GB) | Opslag (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Windows-container groepen

De volgende regio's en maximum resources zijn beschikbaar voor container groepen met [ondersteunde en preview](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server-containers.

| Regio | Maximale CPU voor Windows Server 2016 | Maximale hoeveelheid geheugen (GB) voor Windows Server 2016 | Windows Server 2019 LTSC maximum CPU | Windows Server 2019 LTSC Max. geheugen (GB) | Opslag (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Australië - oost | 2 | 3,5 | 4 | 16 | 20 |
| Brazil South | 4 | 16 | 4 | 16 | 20 |
| Canada - midden | 2 | 3,5 | 4 | 16 | 20 |
| India - centraal | 2 | 3,5 | 4 | 16 | 20 |
| Central US | 2 | 3,5 | 4 | 16 | 20 |
| Azië - oost | 2 | 3,5 | 4 | 16 | 20 |
| VS - oost | 2 | 8 | 4 | 16 | 20 |
| US - oost 2 | 2 | 3,5 | 2 | 3,5 | 20 |
| Frankrijk - centraal | 4 | 16 | 4 | 16 | 20 |
| Japan - oost | 4 | 16 | 4 | 16 | 20 |
| Korea - centraal | 4 | 16 | 4 | 16 | 20 |
| VS - noord-centraal | 2 | 3,5 | 4 | 16 | 20 |
| Europa - noord | 2 | 3,5 | 4 | 16 | 20 |
| South Central US | 2 | 3,5 | 4 | 16 | 20 |
| India - zuid | 2 | 3,5 | 4 | 16 | 20 |
| Azië - zuidoost | 2 | 3,5 | 4 | 16 | 20 |
| Verenigd Koninkrijk Zuid | 2 | 3,5 | 4 | 16 | 20 |
| VS - west-centraal | 4 | 16 | 4 | 16 | 20 |
| Europa -west | 4 | 16 | 4 | 16 | 20 |
| VS - west | 4 | 14 | N.v.t. | N.v.t. | 20 |
| West US 2 | 2 | 3,5 | 2 | 3,5 | 20 |

## <a name="next-steps"></a>Volgende stappen

Laat het team weten of u extra regio's of meer Beschik baarheid van resources wilt zien op [aka.MS/ACI/feedback](https://aka.ms/aci/feedback).

Zie [problemen met implementaties oplossen met Azure container instances](container-instances-troubleshooting.md)voor meer informatie over het oplossen van problemen met de implementatie van container instanties.


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
