---
title: Een Windows VM-resource verplaatsen in azure
description: Een Windows-VM verplaatsen naar een ander Azure-abonnement of resource groep in het Resource Manager-implementatie model.
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919563"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Een Windows-VM verplaatsen naar een ander Azure-abonnement of een andere resource groep
In dit artikel wordt uitgelegd hoe u een virtuele Windows-machine (VM) verplaatst tussen resource groepen of abonnementen. Het verplaatsen tussen abonnementen kan handig zijn als u een virtuele machine in een persoonlijk abonnement hebt gemaakt en u deze nu wilt verplaatsen naar het abonnement van uw bedrijf om door te gaan met uw werk. U hoeft de virtuele machine niet te starten om deze te verplaatsen en deze moet tijdens de verplaatsing blijven worden uitgevoerd.

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
