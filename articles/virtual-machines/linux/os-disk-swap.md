---
title: Os-schijf ruilen voor een Azure-vm met CLI '
description: Wijzig de schijf van het besturingssysteem die door een virtuele Azure-machine wordt gebruikt met behulp van de CLI.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: bfbe47fb68ffe7cee1ee2f9f7b94b418d8da2a1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035332"
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
