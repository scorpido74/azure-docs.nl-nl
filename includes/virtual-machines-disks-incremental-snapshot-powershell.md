---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299449"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Ondersteunde regio’s
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

U Azure PowerShell gebruiken om een incrementele momentopname te maken. U hebt de nieuwste versie van Azure PowerShell nodig, de volgende opdracht installeert deze of werkt uw bestaande installatie bij naar de laatste:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Zodra dat is geïnstalleerd, log je `az login`in op je PowerShell-sessie met .

Als u een incrementele momentopname wilt maken met Azure PowerShell, `-Incremental` stelt u de configuratie met [Nieuw-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) in met de parameter en geeft u die vervolgens als variabele door aan [Nieuw-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) via de `-Snapshot` parameter.

Vervang `<yourDiskNameHere>` `<yourResourceGroupNameHere>`, `<yourDesiredSnapShotNameHere>` en met uw waarden, dan u het volgende script gebruiken om een incrementele momentopname te maken:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

U incrementele momentopnamen van `SourceResourceId` dezelfde `SourceUniqueId` schijf identificeren met de eigenschappen en de eigenschappen van momentopnamen. `SourceResourceId`is de Azure Resource Manager-bron-id van de bovenliggende schijf. `SourceUniqueId`is de waarde die `UniqueId` is overgenomen van de eigenschap van de schijf. Als u een schijf verwijdert en vervolgens een nieuwe schijf met `UniqueId` dezelfde naam maakt, verandert de waarde van de eigenschap.

U kunt `SourceResourceId` `SourceUniqueId` een lijst met alle momentopnamen die aan een bepaalde schijf zijn gekoppeld, gebruiken en maken. Vervang `<yourResourceGroupNameHere>` door uw waarde en vervolgens u het volgende voorbeeld gebruiken om uw bestaande incrementele momentopnamen weer te geven:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Resource Manager-sjabloon

U azure resource beheersjablonen ook gebruiken om een incrementele momentopname te maken. U moet ervoor zorgen dat de apiVersie is ingesteld op **2019-03-01** en dat de incrementele eigenschap ook is ingesteld op true. Het volgende fragment is een voorbeeld van het maken van een incrementele momentopname met Resource Manager-sjablonen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Managed Disks back-ups kopiëren naar een andere regio met differentiële mogelijkheden van incrementele momentopnamen](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)als u voorbeeldcode wilt zien die de differentiële mogelijkheid van incrementele momentopnamen weergeeft.
