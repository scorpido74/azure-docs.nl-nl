---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 26e76731f663ac9038bc87182d52c4bd245f1b6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471678"
---
## <a name="limitations"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Schijfformaten

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Een gedeelde Azure-schijf implementeren

Als u een beheerde schijf wilt implementeren waarbij `maxShares` de functie `>1`gedeelde schijf is ingeschakeld, gebruikt u de nieuwe eigenschap en definieert u een waarde. Dit maakt de schijf deelbaar over meerdere VM's.

> [!IMPORTANT]
> De waarde `maxShares` van kan alleen worden ingesteld of gewijzigd wanneer een schijf is losgekoppeld van alle VM's. Zie de [schijfgroottes](#disk-sizes) voor `maxShares`de toegestane waarden voor .

Voordat u de volgende `[parameters('dataDiskName')]` `[resourceGroup().location]`sjabloon `[parameters('dataDiskSizeGB')]`gebruikt, vervangt u , en `[parameters('maxShares')]` met uw eigen waarden.

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

### <a name="using-azure-shared-disks-with-your-vms"></a>Gedeelde Azure-schijven gebruiken met uw VM's

Zodra u een gedeelde schijf `maxShares>1`hebt geïmplementeerd met, u de schijf op een of meer van uw VM's monteren.

> [!IMPORTANT]
> Alle VM's die een schijf delen, moeten worden geïmplementeerd in dezelfde [plaatsingsgroep voor nabijheid](../articles/virtual-machines/windows/proximity-placement-groups.md).

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

Zodra u de gedeelde schijf op uw VM's in uw cluster hebt gemonteerd, u quorum en lezen/schrijven naar de schijf instellen met Behulp van SCSI PR. De volgende PR-opdrachten zijn beschikbaar bij het gebruik van gedeelde Azure-schijven:

Als u met de schijf wilt communiceren, begint u met de lijst met actie voor permanente reserveringen:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Wanneer u PR_RESERVE, PR_PREEMPT_RESERVATION of PR_RELEASE_RESERVATION gebruikt, een van de volgende persistent-reservation-type:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

U moet ook een permanente reserveringssleutel verstrekken bij het gebruik van PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION of PR_RELEASE-RESERVERING.


## <a name="next-steps"></a>Volgende stappen

Als je geïnteresseerd bent in gedeelde schijven, [meld je dan aan voor onze preview.](https://aka.ms/AzureSharedDiskPreviewSignUp)