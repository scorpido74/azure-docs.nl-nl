---
title: Een Windows VM-bron verplaatsen in Azure
description: Een Windows-vm verplaatsen naar een ander Azure-abonnement of een andere brongroep in het implementatiemodel voor ResourceBeheer.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: 0c3f5541405d1fd983bbf988b99d2b4e10d8908c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865665"
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
