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
ms.openlocfilehash: ee8a711a867f8abdc831b0d1d9d0b504b1104955
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310115"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>Een incrementele moment opname (preview) maken voor beheerde schijven

Incrementele moment opnamen (preview) zijn punt-in-time-back-ups voor beheerde schijven die, wanneer ze worden gemaakt, alleen van alle wijzigingen bestaan sinds de laatste moment opname. Wanneer u probeert een incrementele moment opname te downloaden of anderszins te gebruiken, wordt de volledige VHD gebruikt. Deze nieuwe mogelijkheid voor moment opnamen van beheerde schijven kan ertoe leiden dat ze rendabeler worden, omdat u niet langer de volledige schijf hoeft op te slaan bij elke afzonderlijke moment opname, tenzij u dat doet. Net als bij gewone moment opnamen kunnen incrementele moment opnamen worden gebruikt voor het maken van een volledige beheerde schijf of, om een gewone moment opname te maken.

Er zijn enkele verschillen tussen een incrementele moment opname en een reguliere moment opname. In incrementele moment opnamen wordt altijd standaard Hdd's-opslag gebruikt, ongeacht het opslag type van de schijf, terwijl reguliere moment opnamen Premium Ssd's kunnen gebruiken. Als u gewone moment opnamen op Premium Storage gebruikt voor het opschalen van VM-implementaties, raden we u aan om aangepaste installatie kopieën voor standaard opslag te gebruiken in de [Galerie met gedeelde installatie kopieën](../articles/virtual-machines/linux/shared-image-galleries.md). Het helpt u een grotere schaal te creëren met lagere kosten. Daarnaast bieden incrementele moment opnamen mogelijk betere betrouw baarheid met [zone-redundante opslag](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Als ZRS beschikbaar is in de geselecteerde regio, wordt ZRS automatisch door een incrementele moment opname gebruikt. Als ZRS niet beschikbaar is in de regio, wordt de moment opname standaard ingesteld op [lokaal redundante opslag](../articles/storage/common/storage-redundancy-lrs.md) (LRS). U kunt dit gedrag negeren en het hand matig selecteren, maar dit wordt niet aangeraden.

Incrementele moment opnamen bieden ook een andere mogelijkheid, die uniek is voor beheerde schijven. Hiermee kunt u de wijzigingen ophalen tussen twee incrementele moment opnamen van dezelfde beheerde schijven, tot aan het blok niveau. U kunt deze mogelijkheid gebruiken om uw gegevensfootprint te verminderen bij het kopiëren van moment opnamen tussen regio's.

Als u zich nog niet hebt geregistreerd voor de preview-versie en u wilt beginnen met het gebruik van incrementele moment opnamen AzureDisks@microsoft.com , kunt u een e-mail sturen naar om toegang te krijgen tot de open bare preview.

## <a name="restrictions"></a>Beperkingen

- Incrementele moment opnamen zijn momenteel alleen beschikbaar in West-Centraal vs.
- Incrementele moment opnamen kunnen momenteel niet worden gemaakt nadat u de grootte van een schijf hebt gewijzigd.
- Incrementele moment opnamen kunnen momenteel niet worden verplaatst tussen abonnementen.
- U kunt momenteel alleen SAS-Uri's van Maxi maal vijf moment opnamen van een bepaalde momentopname familie genereren op een bepaald moment.
- U kunt geen incrementele moment opname maken voor een bepaalde schijf buiten het abonnement van de schijf.
- Er kunnen Maxi maal zeven incrementele moment opnamen per schijf worden gemaakt om de vijf minuten.
- Er kunnen voor één schijf een totaal van 200 incrementele moment opnamen worden gemaakt.

## <a name="powershell"></a>PowerShell

U kunt Azure PowerShell gebruiken om een incrementele moment opname te maken. U hebt de nieuwste versie van Azure PowerShell nodig. de volgende opdracht wordt geïnstalleerd of de bestaande installatie wordt bijgewerkt naar de laatste:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Als dat is gebeurd, meldt u zich aan bij `az login`uw Power shell-sessie met.

Als u een incrementele moment opname met Azure PowerShell wilt maken, stelt u de configuratie met [New-AzSnapShotConfig](https://docs.microsoft.com/en-us/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) met de `-Incremental` para meter in en geeft u die als een variabele `-Snapshot` door aan [New-AzSnapshot](https://docs.microsoft.com/en-us/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) door de para meter.

Vervang `<yourDiskNameHere>`, `<yourResourceGroupNameHere>`, en`<yourDesiredSnapShotNameHere>` met uw waarden, vervolgens kunt u het volgende script gebruiken om een incrementele moment opname te maken:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

U kunt incrementele moment opnamen identificeren van dezelfde schijf met de `SourceResourceId` en de `SourceUniqueId` eigenschappen van moment opnamen. `SourceResourceId`is de Azure Resource Manager Resource-ID van de bovenliggende schijf. `SourceUniqueId`is de waarde die wordt overgenomen `UniqueId` van de eigenschap van de schijf. Als u een schijf verwijdert en vervolgens een nieuwe schijf met dezelfde naam maakt, verandert de waarde van de `UniqueId` eigenschap.

U kunt en `SourceResourceId` `SourceUniqueId` gebruiken om een lijst te maken met alle moment opnamen die zijn gekoppeld aan een bepaalde schijf. Vervang `<yourResourceGroupNameHere>` door uw waarde en u kunt het volgende voor beeld gebruiken om uw bestaande incrementele moment opnamen weer te geven:

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

## <a name="cli"></a>CLI

U kunt een incrementele moment opname maken met de Azure CLI. u hebt de nieuwste versie van Azure CLI nodig. Met de volgende opdracht wordt de bestaande installatie geïnstalleerd of bijgewerkt naar de meest recente versie:

```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```

Als u een incrementele moment opname wilt maken, gebruikt u [AZ snap shot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) met de `--incremental` para meter.

In het volgende voor beeld wordt een incrementele moment `<yourDesiredSnapShotNameHere>`opname `<yourResourceGroupNameHere>`gemaakt, vervangen `<exampleLocation>` ,,`<exampleDiskName>`, en met uw eigen waarden, waarna u het voor beeld uitvoert:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

U kunt incrementele moment opnamen identificeren van dezelfde schijf met de `SourceResourceId` en de `SourceUniqueId` eigenschappen van moment opnamen. `SourceResourceId`is de Azure Resource Manager Resource-ID van de bovenliggende schijf. `SourceUniqueId`is de waarde die wordt overgenomen `UniqueId` van de eigenschap van de schijf. Als u een schijf verwijdert en vervolgens een nieuwe schijf met dezelfde naam maakt, verandert de waarde van de `UniqueId` eigenschap.

U kunt en `SourceResourceId` `SourceUniqueId` gebruiken om een lijst te maken met alle moment opnamen die zijn gekoppeld aan een bepaalde schijf. In het volgende voor beeld worden alle incrementele moment opnamen vermeld die zijn gekoppeld aan een bepaalde schijf, maar er is een installatie vereist.

In dit voor beeld wordt JQ gebruikt voor het uitvoeren van query's op de gegevens. Als u het voor beeld wilt uitvoeren, moet u [JQ installeren](https://stedolan.github.io/jq/download/).

Vervang `<yourResourceGroupNameHere>` en`<exampleDiskName>` door uw waarden. vervolgens kunt u het volgende voor beeld gebruiken om uw bestaande incrementele moment opnamen weer te geven, op voor waarde dat u ook JQ hebt geïnstalleerd:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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

## <a name="next-steps"></a>Volgende stappen

Als u zich nog niet hebt geregistreerd voor de preview-versie en u wilt beginnen met het gebruik van incrementele moment opnamen AzureDisks@microsoft.com , kunt u een e-mail sturen naar om toegang te krijgen tot de open bare preview.
