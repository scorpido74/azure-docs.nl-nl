---
title: Azure-netwerk bronnen verplaatsen naar een nieuw abonnement of een nieuwe resource groep
description: Gebruik Azure Resource Manager om virtuele netwerken en andere netwerk bronnen te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: c4e6acb52f6342c57fb1db9fc3e83d90d6d01285
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150838"
---
# <a name="move-guidance-for-networking-resources"></a>Richt lijnen voor netwerk bronnen verplaatsen

In dit artikel wordt beschreven hoe u virtuele netwerken en andere netwerk bronnen verplaatst voor specifieke scenario's.

## <a name="dependent-resources"></a>Afhankelijke resources

Bij het verplaatsen van een virtueel netwerk, moet u ook de afhankelijke resources verplaatsen. Voor VPN-Gateways, moet u IP-adressen, virtuele netwerkgateways en alle bijbehorende verbindingsresources verplaatsen. Lokale netwerkgateways kunnen zich in een andere resourcegroep.

Als u een virtuele machine met een netwerk interface kaart wilt verplaatsen naar een nieuw abonnement, moet u alle afhankelijke resources verplaatsen. Verplaats het virtuele netwerk voor de netwerk interface kaart, alle andere netwerk interface kaarten voor het virtuele netwerk en de VPN-gateways.

Zie [scenario voor verplaatsen tussen abonnementen](../resource-group-move-resources.md#scenario-for-move-across-subscriptions)voor meer informatie.

## <a name="peered-virtual-network"></a>Gekoppeld virtueel netwerk

Voor het verplaatsen van een gekoppeld virtueel netwerk, moet u eerst de virtueel-netwerkpeering uitschakelen. Als uitgeschakeld, kunt u het virtuele netwerk kunt verplaatsen. Na de verplaatsing opnieuw de peering op virtueel netwerk.

## <a name="subnet-links"></a>Subnet koppelingen

U kunt een virtueel netwerk niet verplaatsen naar een ander abonnement, als het virtuele netwerk een subnet met resourcenavigatiekoppelingen bevat. Bijvoorbeeld, als een Azure-Cache voor Redis-resource wordt geïmplementeerd in een subnet, heeft dat subnet een resourcenavigatiekoppeling.

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../resource-group-move-resources.md)voor opdrachten voor het verplaatsen van resources.
