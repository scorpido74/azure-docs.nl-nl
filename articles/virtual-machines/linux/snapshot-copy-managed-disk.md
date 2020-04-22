---
title: Een momentopname van een VHD maken in Azure
description: Meer informatie over het maken van een kopie van een VHD in Azure als back-up of voor het oplossen van problemen.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f2d905d041198bd35c4657052bf18d981904dc44
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758394"
---
# <a name="create-a-snapshot"></a>Een momentopname maken 

Maak een momentopname van een besturingssysteem of gegevensschijf voor back-up of om VM-problemen op te lossen. Een momentopname is een volledige, alleen-lezen kopie van een VHD. 

## <a name="use-azure-cli"></a>Azure CLI gebruiken 

In het volgende voorbeeld moet u [Cloud Shell](https://shell.azure.com/bash) gebruiken of de Azure CLI laten installeren.

In de volgende stappen wordt uitgelegd hoe u een momentopname maakt met de opdracht **AZ-momentopname maken** met de parameter **--source-disk.** In het volgende voorbeeld wordt ervan uitgegaan dat er een VM genaamd *myVM* is in de resourcegroep *myResourceGroup.*

Krijg de schijf-ID met [behulp van az vm show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Maak een momentopname met de naam *osDisk-backup* met behulp van [az snapshot create](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Als u uw momentopname wilt opslaan in zonebestendige opslag, moet u deze maken in een regio die [beschikbaarheidszones](../../availability-zones/az-overview.md) ondersteunt en de **parameter -sku Standard_ZRS** opnemen.

U een lijst met de momentopnamen zien met behulp van [de AZ snapshot list](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Azure Portal gebruiken 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Klik linksboven op **Een resource maken** en zoeken naar **momentopname**. Selecteer **Momentopname** in de zoekresultaten.
3. Klik **in** het blad Momentopname op **Maken**.
4. Voer een **naam** voor de momentopname in.
5. Selecteer een bestaande resourcegroep of typ de naam voor een nieuwe groep. 
7. Selecteer **voor de bronschijf**de beheerde schijf naar momentopname.
8. Selecteer het **accounttype** dat u wilt gebruiken om de momentopname op te slaan. Gebruik **Standard HDD,** tenzij je het nodig hebt opgeslagen op een goed presterende SSD.
9. Klik **op Maken**.


## <a name="next-steps"></a>Volgende stappen

 Maak een virtuele machine op basis van een momentopname door een beheerde schijf te maken vanaf de momentopname en vervolgens de nieuwe beheerde schijf als de OS-schijf te bevestigen. Zie de VM [maken van een momentopname](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) voor meer informatie.

