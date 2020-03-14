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
ms.openlocfilehash: 69be71a58c3aed4f52b77e63c9ddf12365301b08
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299156"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Ondersteunde regio’s
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

U kunt een incrementele moment opname maken met de Azure CLI. u hebt de nieuwste versie van Azure CLI nodig. 

In Windows wordt met de volgende opdracht de bestaande installatie geïnstalleerd of bijgewerkt naar de meest recente versie:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
In Linux is de CLI-installatie afhankelijk van de versie van het besturings systeem.  Zie [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) voor uw specifieke Linux-versie.

Gebruik [AZ snap shot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) met de para meter `--incremental` om een incrementele moment opname te maken.

In het volgende voor beeld wordt een incrementele moment opname gemaakt, `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`,`<exampleDiskName>`en `<exampleLocation>` met uw eigen waarden, en vervolgens het voor beeld uitvoeren:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

U kunt incrementele moment opnamen identificeren van dezelfde schijf met de `SourceResourceId` en de `SourceUniqueId` eigenschappen van moment opnamen. `SourceResourceId` is de Azure Resource Manager Resource-ID van de bovenliggende schijf. `SourceUniqueId` is de waarde die wordt overgenomen van de eigenschap `UniqueId` van de schijf. Als u een schijf verwijdert en vervolgens een nieuwe schijf met dezelfde naam maakt, wordt de waarde van de eigenschap `UniqueId` gewijzigd.

Met `SourceResourceId` en `SourceUniqueId` kunt u een lijst maken met alle moment opnamen die zijn gekoppeld aan een bepaalde schijf. In het volgende voor beeld worden alle incrementele moment opnamen vermeld die zijn gekoppeld aan een bepaalde schijf, maar er is een installatie vereist.

In dit voor beeld wordt JQ gebruikt voor het uitvoeren van query's op de gegevens. Als u het voor beeld wilt uitvoeren, moet u [JQ installeren](https://stedolan.github.io/jq/download/).

Vervang `<yourResourceGroupNameHere>` en `<exampleDiskName>` met uw waarden. vervolgens kunt u het volgende voor beeld gebruiken om uw bestaande incrementele moment opnamen weer te geven, op voor waarde dat u ook JQ hebt geïnstalleerd:

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

Zie [Azure-Managed disks back-ups kopiëren naar een andere regio met differentiële mogelijkheden van incrementele moment opnamen](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)als u voorbeeld code wilt zien met behulp van de differentiële mogelijkheden van incrementele moment opnamen.
