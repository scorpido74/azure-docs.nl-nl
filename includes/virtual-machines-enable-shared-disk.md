---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4487beffd0e6a0643f4d3d88f5bb342f23eb848f
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202253"
---
## <a name="limitations"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Schijf grootten

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Een gedeelde Azure-schijf implementeren

Als u een beheerde schijf wilt implementeren waarop de functie gedeelde schijf is ingeschakeld, gebruikt u de nieuwe eigenschap `maxShares` en definieert u een waarde `>1`. Dit maakt de schijf deelbaar op meerdere Vm's.

> [!IMPORTANT]
> De waarde van `maxShares` kan alleen worden ingesteld of gewijzigd wanneer een schijf is ontkoppeld van alle virtuele machines. Bekijk de [schijf grootten](#disk-sizes) voor de toegestane waarden voor de `maxShares`.

Voordat u de volgende sjabloon gebruikt, vervangt u `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`en `[parameters('maxShares')]` met uw eigen waarden.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Gedeelde Azure-schijven gebruiken met uw Vm's

Wanneer u een gedeelde schijf met `maxShares>1`hebt geïmplementeerd, kunt u de schijf koppelen aan een of meer van uw virtuele machines.

> [!IMPORTANT]
> Alle Vm's die een schijf delen, moeten worden geïmplementeerd in dezelfde [plaatsings groep](../articles/virtual-machines/windows/proximity-placement-groups.md).

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Ondersteunde SCSI PR-opdrachten

Zodra u de gedeelde schijf hebt gekoppeld aan uw virtuele machines in uw cluster, kunt u quorum en lees-/schrijftoegang tot stand brengen met behulp van SCSI-PR. De volgende PR-opdrachten zijn beschikbaar wanneer u gedeelde Azure-schijven gebruikt:

Als u wilt communiceren met de schijf, begint u met de lijst met permanente reserve ringen:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Wanneer u PR_RESERVE, PR_PREEMPT_RESERVATION of PR_RELEASE_RESERVATION gebruikt, geeft u een van de volgende permanente reserve ring-type op:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

U moet ook een permanente reserve ring-sleutel opgeven wanneer u PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION of PR_RELEASE-reserve ring gebruikt.


## <a name="next-steps"></a>Volgende stappen

Als u geïnteresseerd bent in het proberen van gedeelde schijven, [meldt u zich aan voor de preview-versie](https://aka.ms/shareddisksignup).