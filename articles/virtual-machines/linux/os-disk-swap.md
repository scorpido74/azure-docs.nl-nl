---
title: Wisselen tussen OS-schijven met CLI '
description: Wijzig de schijf van het besturingssysteem die door een virtuele Azure-machine wordt gebruikt met behulp van de CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: a8c879ae56eb22d9817841667d33e37109a4f63d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759399"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>De osschijf wijzigen die door een Azure-vm wordt gebruikt met de CLI


Als u een bestaande vm hebt, maar u de schijf wilt ruilen voor een back-upschijf of een andere osschijf, u de Azure CLI gebruiken om de BE-schijven te verwisselen. U hoeft de vm niet te verwijderen en opnieuw te maken. U zelfs een beheerde schijf in een andere resourcegroep gebruiken, zolang deze nog niet in gebruik is.

De VM moet wel worden gestopt\deallocated, waarna de resource-id van de beheerde schijf kan worden vervangen door de resource-id van een andere beheerde schijf. 

Controleer of de VM-grootte en het opslagtype compatibel zijn met de schijf die u wilt koppelen. Als de schijf die u wilt gebruiken bijvoorbeeld in Premium Storage staat, moet de VM in staat zijn om Premium-opslag te gebruiken (zoals een DS-serie).

Dit artikel vereist Azure CLI-versie 2.0.25 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli). 


Gebruik [de lijst met AZ-schijven](/cli/azure/disk) om een lijst met de schijven in uw resourcegroep te krijgen.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Gebruik [de AZ VM-stop](/cli/azure/vm) om de VM te stoppen voordat u de schijven verwisselt.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Az [vm-update gebruiken](/cli/azure/vm#az-vm-update) met de volledige resource-id van de nieuwe schijf voor de `--osdisk` parameter 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Start de VM opnieuw met [de start van az vm](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Volgende stappen**

Zie [Momentopname een schijf](snapshot-copy-managed-disk.md)als u een kopie van een schijf wilt maken.
