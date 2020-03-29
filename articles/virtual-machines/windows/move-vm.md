---
title: Een Windows VM-bron verplaatsen in Azure
description: Een Windows-vm verplaatsen naar een ander Azure-abonnement of een andere brongroep in het implementatiemodel voor ResourceBeheer.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: ed29c92d20a6b0d749ec44a22f42ec446ec58650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919563"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Move a Windows VM to another Azure subscription or resource group (Een Windows-VM verplaatsen naar een ander Azure-abonnement of een andere resourcegroep)
In dit artikel u zich uitleven hoe u een virtuele Windows-machine (VM) verplaatst tussen resourcegroepen of abonnementen. Het verplaatsen van abonnementen kan handig zijn als u oorspronkelijk een VM in een persoonlijk abonnement hebt gemaakt en deze nu wilt verplaatsen naar het abonnement van uw bedrijf om uw werk voort te zetten. U hoeft de VM niet te starten om deze te verplaatsen en deze moet tijdens de verhuizing blijven worden uitgevoerd.

> [!IMPORTANT]
>Nieuwe resource-id's worden gemaakt als onderdeel van de verhuizing. Nadat de VM is verplaatst, moet u uw hulpprogramma's en scripts bijwerken om de nieuwe bron-id's te gebruiken.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Powershell gebruiken om een virtuele machine te verplaatsen

Als u een virtuele machine naar een andere resourcegroep wilt verplaatsen, moet u ervoor zorgen dat u ook alle afhankelijke resources verplaatst. Als u een lijst wilt krijgen met de resource-id van elk van deze resources, gebruikt u de cmdlet [Get-AzResource.](https://docs.microsoft.com/powershell/module/az.resources/get-azresource)

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

U de uitvoer van de vorige opdracht gebruiken om een door komma's gescheiden lijst met bron-id's naar [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) te maken om elke resource naar de bestemming te verplaatsen.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Als u de resources wilt verplaatsen naar een ander abonnement, neemt u de parameter **-DestinationSubscriptionId op.**

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Wanneer u wordt gevraagd te bevestigen dat u de opgegeven resources wilt verplaatsen, voert u **Y** in om te bevestigen.

## <a name="next-steps"></a>Volgende stappen
U veel verschillende typen resources verplaatsen tussen resourcegroepen en abonnementen. Zie [Resources verplaatsen naar een nieuwe resourcegroep of -abonnement voor](../../azure-resource-manager/management/move-resource-group-and-subscription.md)meer informatie.    
