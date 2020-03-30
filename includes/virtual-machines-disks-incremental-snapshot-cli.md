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
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80343058"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

U een incrementele momentopname maken met de Azure CLI, u hebt de nieuwste versie van Azure CLI nodig. 

In Windows wordt uw bestaande installatie met de volgende opdracht geïnstalleerd of bijgewerkt naar de nieuwste versie:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
Op Linux, zal de CLI-installatie variëren afhankelijk van het besturingssysteem versie.  Zie [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) voor uw specifieke Linux-versie.

Als u een incrementele momentopname wilt `--incremental` maken, gebruikt u [az-momentopname maken](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) met de parameter.

In het volgende voorbeeld wordt `<yourDesiredSnapShotNameHere>` `<yourResourceGroupNameHere>`een`<exampleDiskName>`incrementele momentopname gemaakt, wordt het voorbeeld vervangen, vervangen , en `<exampleLocation>` met uw eigen waarden uitgevoerd:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

U incrementele momentopnamen van `SourceResourceId` dezelfde `SourceUniqueId` schijf identificeren met de eigenschappen en de eigenschappen van momentopnamen. `SourceResourceId`is de Azure Resource Manager-bron-id van de bovenliggende schijf. `SourceUniqueId`is de waarde die `UniqueId` is overgenomen van de eigenschap van de schijf. Als u een schijf verwijdert en vervolgens een nieuwe schijf met `UniqueId` dezelfde naam maakt, verandert de waarde van de eigenschap.

U kunt `SourceResourceId` `SourceUniqueId` een lijst met alle momentopnamen die aan een bepaalde schijf zijn gekoppeld, gebruiken en maken. In het volgende voorbeeld worden alle incrementele momentopnamen vermeld die aan een bepaalde schijf zijn gekoppeld, maar deze moet enige installatie vereisen.

In dit voorbeeld wordt jq gebruikt voor het opvragen van de gegevens. Als u het voorbeeld wilt uitvoeren, moet u [jq installeren.](https://stedolan.github.io/jq/download/)

Vervang `<yourResourceGroupNameHere>` `<exampleDiskName>` en met uw waarden, dan u het volgende voorbeeld gebruiken om uw bestaande incrementele momentopnamen weer te geven, zolang u ook jq hebt geïnstalleerd:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
