---
title: Vm's co-lokaliseren voor verbeterde latentie
description: Meer informatie over hoe het co-locating van Azure VM-resources de latentie kan verbeteren.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083169"
---
# <a name="co-locate-resource-for-improved-latency"></a>Co-locate resource voor verbeterde latentie

Wanneer u uw toepassing implementeert in Azure, zorgt het verspreiden van instanties over regio's of beschikbaarheidszones voor netwerklatentie, wat van invloed kan zijn op de algehele prestaties van uw toepassing. 


## <a name="proximity-placement-groups"></a>Nabijheidsplaatsingsgroepen 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Volgende stappen

Implementeer een VM in een [plaatsingsgroep voor nabijheid](proximity-placement-groups.md) met Azure PowerShell.

Meer informatie over het [testen van netwerklatentie](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Meer informatie over het [optimaliseren van de netwerkdoorvoer.](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)  

Meer informatie over het [gebruik van nabijheidsplaatsingsgroepen met SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).