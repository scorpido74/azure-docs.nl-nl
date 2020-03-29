---
title: Inleiding tot de volgende hop in Azure Network Watcher | Microsoft Documenten
description: Dit artikel geeft een overzicht van de Network Watcher next hop mogelijkheid.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 33c22b5b68b6677e8cf271dc185007316ec44500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844055"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Volgende hop gebruiken om problemen met het routeren van virtuele machines te diagnosticeren

Verkeer van een virtuele machine (VM) wordt verzonden naar een bestemming op basis van de effectieve routes die zijn gekoppeld aan een netwerkinterface (NIC). Volgende hop krijgt het volgende hoptype en IP-adres van een pakket van een specifieke VM en NIC. Het kennen van de volgende hop helpt u bepalen of het verkeer wordt doorverwezen naar de beoogde bestemming, of dat het verkeer nergens naartoe wordt gestuurd. Een onjuiste configuratie van routes, waarbij het verkeer naar een on-premises locatie wordt geleid, of een virtueel toestel, kan leiden tot verbindingsproblemen. Volgende hop retourneert ook de routetabel die is gekoppeld aan de volgende hop. Als de route wordt gedefinieerd als een door de gebruiker gedefinieerde route, wordt die route geretourneerd. Anders retourneert de volgende hop **Systeemroute**.

![volgende hop overzicht](./media/network-watcher-next-hop-overview/figure1.png)

De volgende hop die zou kunnen worden geretourneerd door de volgende hop vermogen zijn als volgt:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Geen

Zie [Overzicht van](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)de volgende hop .

## <a name="next-steps"></a>Volgende stappen

Zie Vm-netwerkrouteringsproblemen diagnosticeren met behulp van de [Azure-portal,](diagnose-vm-network-routing-problem.md) [PowerShell](diagnose-vm-network-routing-problem-powershell.md)of Azure [CLI](diagnose-vm-network-routing-problem-cli.md)voor meer informatie over het gebruik van volgende hop om problemen met vm-netwerkroutering te diagnosticeren.
