---
title: Proximity-plaatsings groepen gebruiken voor Windows-Vm's | Microsoft Docs
description: Meer informatie over het maken en gebruiken van proximity-plaatsings groepen voor virtuele Windows-machines in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: af75b3f98232d6507fc8b0fda179bebc75828086
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088825"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-powershell"></a>Preview: Vm's implementeren op proximity-plaatsings groepen met Power shell


Als u virtuele machines zo dicht mogelijk wilt ophalen, moet u deze implementeren in een proximity-plaatsings [groep](co-location.md#preview-proximity-placement-groups).

Een proximity-plaatsings groep is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure Compute-resources zich fysiek dicht bij elkaar bevinden. Proximity-plaatsings groepen zijn handig voor werk belastingen waarbij lage latentie een vereiste is.

> [!IMPORTANT]
> Proximity-plaatsings groepen bevindt zich momenteel in een open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
>
> Proximity-plaatsings groepen zijn niet beschikbaar in deze regio's tijdens de preview-versie: **Japan-Oost**, **Australië-Oost** en **India centraal**.


## <a name="create-a-proximity-placement-group"></a>Een nabijheidsplaatsingsgroep maken
Maak een proximity-plaatsings groep met behulp van de cmdlet [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) . 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Proximity-plaatsings groepen weer geven

U kunt alle proximity-plaatsings groepen weer geven met behulp van de cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) .

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Een virtuele machine maken

Maak een virtuele machine in de plaatsings groep `-ProximityPlacementGroup $ppg.Id` van nabij met om te verwijzen naar de Proximity-groeps-id bij gebruik van [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) om de virtuele machine te maken.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

U kunt de virtuele machine in de plaatsings groep weer geven met behulp van [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

## <a name="availability-sets"></a>Beschikbaarheidssets
U kunt ook een beschikbaarheidsset maken in de plaatsings groep voor proximity. Gebruik dezelfde `-ProximityPlacementGroup` para meter met de cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) voor het maken van een beschikbaarheidsset en alle virtuele machines die zijn gemaakt in de beschikbaarheidsset, worden ook gemaakt in dezelfde plaatsings groep.

## <a name="scale-sets"></a>Schaalsets

U kunt ook een schaalset maken in de plaatsings groep voor proximity. Gebruik dezelfde `-ProximityPlacementGroup` para meter met [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) voor het maken van een schaalset en alle instanties worden gemaakt in dezelfde plaatsings groep.

## <a name="next-steps"></a>Volgende stappen

U kunt ook de [Azure cli](../linux/proximity-placement-groups.md) gebruiken om proximity-plaatsings groepen te maken.
