---
title: Vm's samen zoeken voor verbeterde latentie
description: Meer informatie over hoe co-locaties van Azure-VM-bronnen de latentie kunnen verbeteren.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 85832fe56b064916b50a63bf4b399f0a21afaaac
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028154"
---
# <a name="co-locate-resource-for-improved-latency"></a>Resource samen zoeken voor verbeterde latentie

Wanneer u uw toepassing in azure implementeert, wordt er netwerk latentie gemaakt, wat van invloed kan zijn op de algehele prestaties van uw toepassing. 


## <a name="proximity-placement-groups"></a>Nabijheidsplaatsingsgroepen 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Volgende stappen

Implementeer een virtuele machine op een [proximity-plaatsings groep](proximity-placement-groups.md) met behulp van Azure PowerShell.

Meer informatie over het testen van de [netwerk latentie](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Meer informatie over het optimaliseren van de [netwerk doorvoer](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Meer informatie over het [gebruik van proximity-plaatsings groepen met SAP-toepassingen](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
