---
title: Azure-netwerkbronnen verplaatsen naar een nieuw abonnement of brongroep
description: Gebruik Azure Resource Manager om virtuele netwerken en andere netwerkbronnen te verplaatsen naar een nieuwe brongroep of -abonnement.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485232"
---
# <a name="move-guidance-for-networking-resources"></a>Richtlijnen verplaatsen voor netwerkbronnen

In dit artikel wordt beschreven hoe u virtuele netwerken en andere netwerkbronnen verplaatst voor specifieke scenario's.

## <a name="dependent-resources"></a>Afhankelijke resources

Wanneer u een virtueel netwerk verplaatst, moet u ook de afhankelijke bronnen verplaatsen. Voor VPN-gateways moet u IP-adressen, virtuele netwerkgateways en alle bijbehorende verbindingsbronnen verplaatsen. Lokale netwerkgateways kunnen zich in een andere brongroep bevinden.

Als u een virtuele machine met een netwerkinterfacekaart wilt verplaatsen naar een nieuw abonnement, moet u alle afhankelijke resources verplaatsen. Verplaats het virtuele netwerk voor de netwerkinterfacekaart, alle andere netwerkinterfacekaarten voor het virtuele netwerk en de VPN-gateways.

Zie [Scenario voor het verplaatsen van abonnementen voor](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions)meer informatie .

## <a name="peered-virtual-network"></a>Peered virtueel netwerk

Als u een virtueel netwerk met peered wilt verplaatsen, moet u eerst het virtuele netwerkpeeren uitschakelen. Eenmaal uitgeschakeld, u het virtuele netwerk verplaatsen. Na de verhuizing u het virtuele netwerkpeeren opnieuw inschakelen.

## <a name="subnet-links"></a>Subnetkoppelingen

U een virtueel netwerk niet verplaatsen naar een ander abonnement als het virtuele netwerk een subnet met resourcenavigatiekoppelingen bevat. Als een Azure-cache voor Redis-bron bijvoorbeeld wordt geïmplementeerd in een subnet, heeft dat subnet een koppeling met resourcenavigatie.

## <a name="next-steps"></a>Volgende stappen

Zie Resources verplaatsen naar [een nieuwe resourcegroep of -abonnement](../move-resource-group-and-subscription.md)voor opdrachten om resources te verplaatsen.
