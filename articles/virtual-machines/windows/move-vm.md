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
ms.openlocfilehash: 9264e36922cb88c541ba9fb2fe54a9606f371b72
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033180"
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
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-list -wrap -Property ResourceId 
```

U kunt de uitvoer van de vorige opdracht gebruiken als een door komma's gescheiden lijst met resource-Id's naar [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) om elke resource naar het doel te verplaatsen. 

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
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
U kunt verschillende soorten resources verplaatsen tussen resource groepen en abonnementen. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../resource-group-move-resources.md)voor meer informatie.    

