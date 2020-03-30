---
title: Windows Azure VM's co-lokaliseren
description: Meer informatie over hoe het co-locating van Azure VM-resources de latentie kan verbeteren.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266791"
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