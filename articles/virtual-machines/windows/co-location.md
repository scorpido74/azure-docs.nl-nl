---
title: Vm's samen zoeken voor verbeterde latentie
description: Meer informatie over hoe co-locaties van Azure-VM-bronnen de latentie kunnen verbeteren.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 9c981e9b829fa10f094370e1e1ffd8b6df967d4b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965774"
---
# <a name="co-locate-resource-for-improved-latency"></a>Resource samen zoeken voor verbeterde latentie

Wanneer u uw toepassing in azure implementeert, wordt er netwerk latentie gemaakt, wat van invloed kan zijn op de algehele prestaties van uw toepassing. 


## <a name="proximity-placement-groups"></a>Nabijheidsplaatsingsgroepen 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Volgende stappen

Implementeer een virtuele machine op een [proximity-plaatsings groep](proximity-placement-groups.md) met behulp van Azure PowerShell.

Meer informatie over het testen van de [netwerk latentie](../../virtual-network/virtual-network-test-latency.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json).

Meer informatie over het optimaliseren van de [netwerk doorvoer](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Meer informatie over het [gebruik van proximity-plaatsings groepen met SAP-toepassingen](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).