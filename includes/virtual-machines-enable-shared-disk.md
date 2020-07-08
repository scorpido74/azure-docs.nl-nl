---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0df74b82c847c9738d97d2001573666714c17672
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81008328"
---
## <a name="limitations"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Schijf grootten

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Gedeelde schijven implementeren

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Een Premium SSD implementeren als een gedeelde schijf

Voor het implementeren van een beheerde schijf waarop de functie gedeelde schijf is ingeschakeld, gebruikt u de nieuwe eigenschap `maxShares` en definieert u een waarde die groter is dan 1. Dit maakt de schijf deelbaar op meerdere Vm's.

> [!IMPORTANT]
> De waarde van `maxShares` kan alleen worden ingesteld of gewijzigd wanneer een schijf van alle virtuele machines wordt ontkoppeld. Zie de [schijf grootten](#disk-sizes) voor de toegestane waarden voor `maxShares` .

Vervang `[parameters('dataDiskName')]` ,, `[resourceGroup().location]` en door `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` uw eigen waarden voordat u de volgende sjabloon gebruikt.

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

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Een ultra schijf implementeren als een gedeelde schijf

#### <a name="cli"></a>CLI

Als u een beheerde schijf met de functie gedeelde schijf wilt implementeren, wijzigt `maxShares` u de para meter in een waarde die groter is dan 1. Dit maakt de schijf deelbaar op meerdere Vm's.

> [!IMPORTANT]
> De waarde van `maxShares` kan alleen worden ingesteld of gewijzigd wanneer een schijf van alle virtuele machines wordt ontkoppeld. Zie de [schijf grootten](#disk-sizes) voor de toegestane waarden voor `maxShares` .

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Voor het implementeren van een beheerde schijf waarop de functie gedeelde schijf is ingeschakeld, gebruikt u de eigenschap `maxShares` en definieert u een waarde die groter is dan 1. Dit maakt de schijf deelbaar op meerdere Vm's.

> [!IMPORTANT]
> De waarde van `maxShares` kan alleen worden ingesteld of gewijzigd wanneer een schijf van alle virtuele machines wordt ontkoppeld. Zie de [schijf grootten](#disk-sizes) voor de toegestane waarden voor `maxShares` .

Vervang,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` en `[parameters('diskMBpsReadOnly')]` door uw eigen waarden voordat u de volgende sjabloon gebruikt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
        }
    }
  ]
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Gedeelde Azure-schijven gebruiken met uw Vm's

Wanneer u een gedeelde schijf met hebt geïmplementeerd `maxShares>1` , kunt u de schijf koppelen aan een of meer van uw virtuele machines.

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

Als u geïnteresseerd bent in het proberen van gedeelde schijven, [meldt u zich aan voor de preview-versie](https://aka.ms/AzureSharedDiskPreviewSignUp).