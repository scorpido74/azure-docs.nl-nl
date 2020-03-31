---
title: Voorbeeld van plaatsingsgroepen voor virtuele machineschalen
description: Meer informatie over het maken en gebruiken van plaatsingsgroepen voor virtuele machineschalen van Windows in Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273623"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Voorbeeld: Groepen voor nabijheidsplaatsing maken en gebruiken met PowerShell

Als u VM's zo dicht mogelijk wilt plaatsen en de laagst mogelijke latentie wilt bereiken, moet u uw schaalset implementeren binnen een [plaatsingsgroep voor nabijheid.](co-location.md#preview-proximity-placement-groups)

Een plaatsingsgroep voor nabijheid is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure-rekenbronnen fysiek dicht bij elkaar staan. Plaatsingsgroepen voor nabijheidzijn handig voor workloads waarbij lage latentie een vereiste is.

> [!IMPORTANT]
> Nabijheidsplaatsingsgroepen staan momenteel in een openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
>
> Nabijheidsplaatsingsgroepen zijn niet beschikbaar in deze regio's tijdens de preview: **Japan East**, **Australia East** en India **Central**.


## <a name="create-a-proximity-placement-group"></a>Een nabijheidsplaatsingsgroep maken
Maak een plaatsingsgroep voor nabijheid met de cmdlet [Nieuw-AzProximityPlacementGroep.](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) 

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

## <a name="list-proximity-placement-groups"></a>Plaatsingsgroepen voor nabijheid weergeven

U alle nabijheidsplaatsingsgroepen weergeven met de cmdlet [Get-AzProximityPlacementGroup.](/powershell/module/az.compute/get-azproximityplacementgroup)

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Een schaalset maken

Maak een schaal in de `-ProximityPlacementGroup $ppg.Id` groep nabijheidsplaatsing met behulp van de groeps-id voor plaatsing in de nabijheid wanneer u [Nieuw-AzVMSS gebruikt](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) om de schaalset te maken.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

U de instantie in de plaatsingsgroep zien met [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Volgende stappen

U de [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) ook gebruiken om nabijheidsplaatsingsgroepen te maken.
