---
title: Een moment opname van een VHD maken in azure
description: Meer informatie over het maken van een kopie van een VHD in azure als back-up of voor het oplossen van problemen.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f2d905d041198bd35c4657052bf18d981904dc44
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758394"
---
# <a name="create-a-snapshot"></a>Een momentopname maken 

Maak een moment opname van een besturings systeem of gegevens schijf voor back-up of om problemen met VM'S op te lossen. Een moment opname is een volledige, alleen-lezen kopie van een VHD. 

## <a name="use-azure-cli"></a>Azure CLI gebruiken 

In het volgende voor beeld moet u [Cloud shell](https://shell.azure.com/bash) gebruiken of moet de Azure cli zijn geïnstalleerd.

De volgende stappen laten zien hoe u een moment opname maakt met behulp van de opdracht **AZ snap shot Create** met de para meter **--Source-Disk** . In het volgende voor beeld wordt ervan uitgegaan dat er een virtuele machine met de naam *myVM* in de resource groep *myResourceGroup* .

Gebruik [AZ VM show](/cli/azure/vm#az-vm-show)om de schijf-id op te halen.

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Maak een moment opname met de naam *osDisk-backup* met behulp van [AZ snap shot Create](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Als u uw moment opname wilt opslaan in zone-flexibele opslag, moet u deze maken in een regio die [beschikbaarheids zones](../../availability-zones/az-overview.md) ondersteunt en de para meter **--SKU Standard_ZRS** bevatten.

U kunt een lijst met de moment opnamen zien met de [lijst AZ snap shot](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Azure Portal gebruiken 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Klik in de linkerbovenhoek op **een resource maken** en zoek naar een **moment opname**. Selecteer **moment opname** in de zoek resultaten.
3. Klik op de Blade **moment opname** op **maken**.
4. Voer een **naam** in voor de moment opname.
5. Selecteer een bestaande resource groep of typ de naam van een nieuwe. 
7. Voor de **bron schijf**selecteert u de beheerde schijf voor de moment opname.
8. Het **account type** selecteren dat moet worden gebruikt voor het opslaan van de moment opname. Gebruik **Standard-HDD** tenzij u het hebt opgeslagen op een high-upssd.
9. Klik op **maken**.


## <a name="next-steps"></a>Volgende stappen

 Een virtuele machine maken op basis van een moment opname door een beheerde schijf te maken op basis van de moment opname en vervolgens de nieuwe beheerde schijf als de besturingssysteem schijf te koppelen. Zie voor meer informatie het script [een virtuele machine maken op basis van een moment opname](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) .

