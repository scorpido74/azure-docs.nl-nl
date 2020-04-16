---
title: Beschikbaarheid van resources per regio
description: Beschikbaarheid van reken- en geheugenbronnen voor de Azure Container Instances-service in verschillende Azure-regio's.
ms.topic: article
ms.date: 02/19/2020
ms.author: danlep
ms.openlocfilehash: c84ab7833f7c90d5d4b3c340c268fd9f75e20da9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399403"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Beschikbaarheid van resources voor Azure Container Instances in Azure-gebieden

In dit artikel wordt de beschikbaarheid van gegevensgegevens, geheugen- en opslagbronnen voor Azure-containers en het doelbesturingssysteem beschreven. 

De gepresenteerde waarden zijn de maximaal beschikbare resources per implementatie van een [containergroep](container-instances-container-groups.md). Waarden zijn actueel op het moment van publicatie. 

> [!NOTE]
> Containergroepen die binnen deze resourcelimieten zijn gemaakt, zijn afhankelijk van beschikbaarheid binnen het implementatiegebied. Wanneer een regio zwaar wordt belast, kan er een fout optreden bij het implementeren van instanties. Als u een dergelijke implementatiefout wilt voorkomen, probeert u instanties met lagere resourceinstellingen te implementeren of probeert u uw implementatie op een later tijdstip of in een andere regio met beschikbare resources.

Zie [Quota en limieten voor Azure Container Instances voor](container-instances-quotas.md)informatie over quota en andere limieten in uw implementaties.

## <a name="availability---general"></a>Beschikbaarheid - Algemeen

De volgende regio's en maximale resources zijn beschikbaar voor containergroepen met Linux en [ondersteunde](container-instances-faq.md#what-windows-base-os-images-are-supported) containers op basis van Windows Server 2016.

| Regio's | OS | Maximaal CPU-gebruik | Maximaal geheugen (GB) | Opslag (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Brazilië Zuid, Canada Centraal, Centraal-India, Centraal-VS, Oost-Azië, Oost-VS, Oost-VS 2, Noord-Europa, Zuid-Centraal VS, Zuidoost-Azië, Zuid-India, Verenigd Koninkrijk Zuid, West-Europa, West-VS, West-VS 2 | Linux | 4 | 16 | 50 |
| Australië Oost, Japan Oost | Linux | 2 | 8 | 50 |
| VS - noord-centraal | Linux | 2 | 3,5 | 50 |
| Brazilië Zuid, Japan Oost, West-Europa | Windows | 4 | 16 | 20 |
| Oost-VS, West-VS | Windows | 4 | 14 | 20 |
| Australië Oost, Canada Centraal, Centraal-India, Centraal-VS, Oost-Azië, Oost-VS 2, Noord-Centraal VS, Noord-Europa, Zuid-Centraal VS, Zuidoost-Azië, Zuid-India, Het Verenigd Koninkrijk Zuid, West-VS 2 | Windows | 2 | 3,5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Beschikbaarheid - Windows Server 2019 LTSC, 1809-implementaties (voorbeeld)

De volgende regio's en maximale resources zijn beschikbaar voor containergroepen met op Windows Server 2019 gebaseerde containers (voorbeeld).

| Regio's | OS | Maximaal CPU-gebruik | Maximaal geheugen (GB) | Opslag (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Australië Oost, Brazilië Zuid, Canada Centraal, Centraal-India, Centraal-VS, Oost-Azië, Oost-VS, Japan-Oost, Noord-Centraal VS, Noord-Europa, Zuid-Centraal VS, Zuidoost-Azië, Zuid-India, Verenigd Koninkrijk Zuid, West-Europa | Windows | 4 | 16 | 20 |
| Oost VS 2, West US 2 | Windows | 2 | 3,5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Beschikbaarheid - Virtuele netwerkimplementatie

De volgende regio's en maximale resources zijn beschikbaar voor een containergroep die is geïmplementeerd in een [virtueel Azure-netwerk.](container-instances-vnet.md)

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Beschikbaarheid - GPU-bronnen (voorbeeld)

De volgende regio's en maximale resources zijn beschikbaar voor een containergroep die is geïmplementeerd met [GPU-bronnen](container-instances-gpu.md) (voorbeeld).

> [!IMPORTANT]
> GPU-bronnen zijn alleen beschikbaar op aanvraag. Als u toegang wilt vragen tot GPU-bronnen, dient u een [Azure-ondersteuningsaanvraag][azure-support]in.

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Volgende stappen

Laat het team weten of u extra regio's of meer beschikbaarheid van resources op [aka.ms/aci/feedback](https://aka.ms/aci/feedback)wilt zien.

Zie [Problemen met implementatie problemen met Azure Container Instances oplossen](container-instances-troubleshooting.md)voor informatie over het oplossen van problemen met containerexemplaren.


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
