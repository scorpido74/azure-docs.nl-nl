---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e39f294f7902eabef401d4c8145f4f19a07f267f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224570"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>Een incrementele moment opname (preview) maken voor beheerde schijven

Incrementele moment opnamen (preview) zijn punt-in-time-back-ups voor beheerde schijven die, wanneer ze worden gemaakt, alleen van alle wijzigingen bestaan sinds de laatste moment opname. Wanneer u probeert een incrementele moment opname te downloaden of anderszins te gebruiken, wordt de volledige VHD gebruikt. Deze nieuwe mogelijkheid voor moment opnamen van beheerde schijven kan ertoe leiden dat ze rendabeler worden, omdat u niet langer de volledige schijf hoeft op te slaan bij elke afzonderlijke moment opname, tenzij u dat doet. Net als bij gewone moment opnamen kunnen incrementele moment opnamen worden gebruikt voor het maken van een volledige beheerde schijf of, om een gewone moment opname te maken.

Er zijn enkele verschillen tussen een incrementele moment opname en een reguliere moment opname. In incrementele moment opnamen wordt altijd standaard Hdd's-opslag gebruikt, ongeacht het opslag type van de schijf, terwijl reguliere moment opnamen Premium Ssd's kunnen gebruiken. Als u gewone moment opnamen op Premium Storage gebruikt voor het opschalen van VM-implementaties, raden we u aan om aangepaste installatie kopieën voor standaard opslag te gebruiken in de [Galerie met gedeelde installatie kopieën](../articles/virtual-machines/linux/shared-image-galleries.md). Het helpt u een grotere schaal te creëren met lagere kosten. Daarnaast bieden incrementele moment opnamen mogelijk betere betrouw baarheid met [zone-redundante opslag](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Als ZRS beschikbaar is in de geselecteerde regio, wordt ZRS automatisch door een incrementele moment opname gebruikt. Als ZRS niet beschikbaar is in de regio, wordt de moment opname standaard ingesteld op [lokaal redundante opslag](../articles/storage/common/storage-redundancy-lrs.md) (LRS). U kunt dit gedrag negeren en het hand matig selecteren, maar dit wordt niet aangeraden.

Incrementele moment opnamen bieden ook een andere mogelijkheid, die uniek is voor beheerde schijven. Hiermee kunt u de wijzigingen ophalen tussen twee incrementele moment opnamen van dezelfde beheerde schijven, tot aan het blok niveau. U kunt deze mogelijkheid gebruiken om uw gegevensfootprint te verminderen bij het kopiëren van moment opnamen tussen regio's.

Als u zich nog niet hebt geregistreerd voor de preview-versie en u wilt beginnen met het gebruik van incrementele moment opnamen, AzureDisks@microsoft.com stuur dan een e-mail naar om toegang te krijgen tot de open bare preview.

## <a name="restrictions"></a>Beperkingen

- Incrementele moment opnamen kunnen momenteel niet worden gemaakt nadat u de grootte van een schijf hebt gewijzigd.
- Incrementele moment opnamen kunnen momenteel niet worden verplaatst tussen abonnementen.
- U kunt momenteel alleen SAS-Uri's van Maxi maal vijf moment opnamen van een bepaalde momentopname familie genereren op een bepaald moment.
- U kunt geen incrementele moment opname maken voor een bepaalde schijf buiten het abonnement van de schijf.
- Er kunnen Maxi maal zeven incrementele moment opnamen per schijf worden gemaakt om de vijf minuten.
- Er kunnen voor één schijf een totaal van 200 incrementele moment opnamen worden gemaakt.

## <a name="powershell"></a>PowerShell

U kunt Azure PowerShell gebruiken om een incrementele moment opname te maken. U kunt de nieuwste versie van Power shell lokaal installeren. U hebt de nieuwste versie van Azure PowerShell nodig. de volgende opdracht wordt geïnstalleerd of de bestaande installatie wordt bijgewerkt naar de laatste:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Als dat is gebeurd, meldt u zich aan bij `az login`uw Power shell-sessie met.

Vervang `<yourDiskNameHere>`, `<yourResourceGroupNameHere>`, en`<yourDesiredSnapShotNameHere>` met uw waarden, vervolgens kunt u het volgende script gebruiken om een incrementele moment opname te maken:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting:
# 1. Incremental property
# 2. SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 

# You can identify incremental snapshots of the same disk by using the SourceResourceId and SourceUniqueId properties of snapshots. 
# SourceResourceId is the Azure Resource Manager resource ID of the parent disk. 
# SourceUniqueId is the value inherited from the UniqueId property of the disk. If you delete a disk and then create a disk with the same name, the value of the UniqueId property will change. 
# Following script shows how to get all the incremental snapshots in a resource group of same disk
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

U kunt ook Azure Resource Manager sjablonen gebruiken om een incrementele moment opname te maken. U moet ervoor zorgen dat de apiVersion is ingesteld op **2019-03-01** en dat de incrementele eigenschap ook is ingesteld op True. Het volgende code fragment is een voor beeld van het maken van een incrementele moment opname met Resource Manager-sjablonen:

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

## <a name="cli"></a>CLI

U kunt een incrementele moment opname maken met de Azure CLI met behulp van [AZ snap shot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create). Een voor beeld van een opdracht zou er als volgt uitzien:

```bash
az snapshot create -g <exampleResourceGroup> \
-n <exampleSnapshotName> \
-l <exampleLocation> \
--source <exampleVMId> \
--incremental
```

U kunt ook identificeren welke moment opnamen incrementele moment opnamen zijn in de CLI met door gebruik te `--query` maken van de para meter op [AZ snap shot show](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-show). U kunt deze para meter gebruiken om de eigenschappen **SourceResourceId** en **SourceUniqueId** van moment opnamen rechtstreeks op te vragen. SourceResourceId is de Azure Resource Manager Resource-ID van de bovenliggende schijf. **SourceUniqueId** is de waarde die wordt overgenomen van de eigenschap **UniqueId** van de schijf. Als u een schijf verwijdert en vervolgens een schijf met dezelfde naam maakt, wordt de waarde van de eigenschap **UniqueId** gewijzigd.

Voor beelden van beide query's zijn als volgt:

```bash
az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceResourceId] -o tsv

az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceUniqueId] -o tsv
```

## <a name="next-steps"></a>Volgende stappen

Als u zich nog niet hebt geregistreerd voor de preview-versie en u wilt beginnen met het gebruik van incrementele moment opnamen, AzureDisks@microsoft.com stuur dan een e-mail naar om toegang te krijgen tot de open bare preview.
