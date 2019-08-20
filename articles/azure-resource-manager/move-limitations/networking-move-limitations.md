---
title: Azure-netwerk bronnen verplaatsen naar een nieuw abonnement of een nieuwe resource groep | Microsoft Docs
description: Gebruik Azure Resource Manager om virtuele netwerken en andere netwerk bronnen te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: c41ab1c3309243fa3a96d907f620ffeff10376a1
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626260"
---
# <a name="move-guidance-for-networking-resources"></a>Richt lijnen voor netwerk bronnen verplaatsen

In dit artikel wordt beschreven hoe u virtuele netwerken en andere netwerk bronnen verplaatst voor specifieke scenario's.

## <a name="dependent-resources"></a>Afhankelijke bronnen

Bij het verplaatsen van een virtueel netwerk, moet u ook de afhankelijke resources verplaatsen. Voor VPN-Gateways, moet u IP-adressen, virtuele netwerkgateways en alle bijbehorende verbindingsresources verplaatsen. Lokale netwerkgateways kunnen zich in een andere resourcegroep.

Als u een virtuele machine met een netwerk interface kaart wilt verplaatsen, moet u alle afhankelijke resources verplaatsen. Verplaats het virtuele netwerk voor de netwerk interface kaart, alle andere netwerk interface kaarten voor het virtuele netwerk en de VPN-gateways.

## <a name="state-of-dependent-resources"></a>Status van afhankelijke resources

Als de bron-of doel resource groep een virtueel netwerk bevat, worden de statussen van alle afhankelijke resources voor het virtuele netwerk gecontroleerd tijdens de verplaatsing. Als een van deze resources een mislukte status heeft, wordt de verplaatsing geblokkeerd. Als bijvoorbeeld een virtuele machine die gebruikmaakt van het virtuele netwerk is mislukt, wordt de verplaatsing geblokkeerd. De verplaatsing wordt geblokkeerd, zelfs wanneer de virtuele machine niet een van de resources is die worden verplaatst en die zich niet in een van de resource groepen bevindt voor de verplaatsing. U kunt dit probleem voor komen door uw resources te verplaatsen naar een resource groep die geen virtueel netwerk heeft.

## <a name="peered-virtual-network"></a>Gekoppeld virtueel netwerk

Voor het verplaatsen van een gekoppeld virtueel netwerk, moet u eerst de virtueel-netwerkpeering uitschakelen. Als uitgeschakeld, kunt u het virtuele netwerk kunt verplaatsen. Na de verplaatsing opnieuw de peering op virtueel netwerk.

## <a name="subnet-links"></a>Subnet koppelingen

U kunt een virtueel netwerk niet verplaatsen naar een ander abonnement, als het virtuele netwerk een subnet met resourcenavigatiekoppelingen bevat. Bijvoorbeeld, als een Azure-Cache voor Redis-resource wordt geïmplementeerd in een subnet, heeft dat subnet een resourcenavigatiekoppeling.

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../resource-group-move-resources.md)voor opdrachten voor het verplaatsen van resources.
