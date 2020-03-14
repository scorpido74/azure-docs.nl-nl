---
title: Windows Azure-Vm's samen zoeken
description: Meer informatie over hoe co-locaties van Azure-VM-bronnen de latentie kunnen verbeteren.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: b61a84f2b284177b7443be00efeea65e7eb8aaef
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266791"
---
# <a name="co-locate-resource-for-improved-latency"></a>Resource samen zoeken voor verbeterde latentie

Wanneer u uw toepassing in azure implementeert, wordt er netwerk latentie gemaakt, wat van invloed kan zijn op de algehele prestaties van uw toepassing. 


## <a name="proximity-placement-groups"></a>Nabijheidsplaatsingsgroepen 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Volgende stappen

Implementeer een virtuele machine op een [proximity-plaatsings groep](proximity-placement-groups.md) met behulp van Azure PowerShell.

Meer informatie over het testen van de [netwerk latentie](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Meer informatie over het optimaliseren van de [netwerk doorvoer](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Meer informatie over het [gebruik van proximity-plaatsings groepen met SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).