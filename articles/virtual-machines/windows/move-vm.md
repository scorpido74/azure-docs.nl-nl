---
title: Een Windows VM-resource verplaatsen in azure
description: Een Windows-VM verplaatsen naar een ander Azure-abonnement of resource groep in het Resource Manager-implementatie model.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: 6e156af2d4805ba310443a2acf6cc47d06a735eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84762323"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Move a Windows VM to another Azure subscription or resource group (Een Windows-VM verplaatsen naar een ander Azure-abonnement of een andere resourcegroep)
In dit artikel wordt uitgelegd hoe u een virtuele Windows-machine (VM) verplaatst tussen resource groepen of abonnementen. Het verplaatsen tussen abonnementen kan handig zijn als u een virtuele machine in een persoonlijk abonnement hebt gemaakt en u deze nu wilt verplaatsen naar het abonnement van uw bedrijf om door te gaan met uw werk. U hoeft de virtuele machine niet te stoppen voordat u deze kunt verplaatsen. de VM moet blijven worden uitgevoerd tijdens de verplaatsing.

> [!IMPORTANT]
>Nieuwe resource-Id's worden gemaakt als onderdeel van de verplaatsing. Nadat de VM is verplaatst, moet u uw hulp middelen en scripts bijwerken om de nieuwe resource-Id's te gebruiken.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Power shell gebruiken om een virtuele machine te verplaatsen

Als u een virtuele machine naar een andere resource groep wilt verplaatsen, moet u ervoor zorgen dat u ook alle afhankelijke resources verplaatst. Als u een lijst met de resource-ID van elk van deze resources wilt ophalen, gebruikt u de cmdlet [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) .

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

U kunt de uitvoer van de vorige opdracht gebruiken om een door komma's gescheiden lijst met resource-Id's te maken en te [verplaatsen-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) om elke resource naar het doel te verplaatsen.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Als u de resources naar een ander abonnement wilt verplaatsen, neemt u de para meter **-DestinationSubscriptionId** op.

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Wanneer u wordt gevraagd om te bevestigen dat u de opgegeven resources wilt verplaatsen, voert u **Y** om te bevestigen.

## <a name="next-steps"></a>Volgende stappen
U kunt verschillende soorten resources verplaatsen tussen resource groepen en abonnementen. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)voor meer informatie.    
