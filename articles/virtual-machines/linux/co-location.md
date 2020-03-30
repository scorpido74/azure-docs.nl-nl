---
title: Linux VM's co-lokaliseren
description: Meer informatie over hoe het co-locating van Azure VM-resources de latentie kan verbeteren.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: d2fd8a2cd7dac7b1d3c78691c84a861d924005ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250281"
---
# <a name="co-locate-resources-for-improved-latency"></a>Co-locate resources voor verbeterde latentie

Wanneer u uw toepassing implementeert in Azure, zorgt het verspreiden van instanties over regio's of beschikbaarheidszones voor netwerklatentie, wat van invloed kan zijn op de algehele prestaties van uw toepassing. 

## <a name="proximity-placement-groups"></a>Nabijheidsplaatsingsgroepen

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Volgende stappen

Een VM implementeren in een [plaatsingsgroep voor nabijheid](proximity-placement-groups.md) met de Azure CLI.

Meer informatie over het [testen van netwerklatentie](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Meer informatie over het [optimaliseren van de netwerkdoorvoer.](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

Meer informatie over het [gebruik van nabijheidsplaatsingsgroepen met SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
