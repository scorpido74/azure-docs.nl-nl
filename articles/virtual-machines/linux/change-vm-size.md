---
title: Het formaat van een Linux-vm wijzigen met de Azure CLI
description: Hoe op te schalen of op te schalen een Linux virtuele machine, door het veranderen van de VM-grootte.
author: mikewasson
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2017
ms.author: mwasson
ms.openlocfilehash: 20e7db80b55347c4a4a76b7c95d4d8bec368abda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969263"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Het formaat van een virtuele Linux-machine wijzigen met behulp van Azure CLI 

Nadat u een virtuele machine (VM) hebt ingemaakt, u de VM omhoog of omlaag schalen door de [VM-grootte][vm-sizes]te wijzigen. In sommige gevallen moet u de VM eerst deallocateeren. U moet de VM detoewijzen als de gewenste grootte niet beschikbaar is op het hardwarecluster dat de VM host. In dit artikel wordt beschreven hoe u het formaat van een Linux-vm wijzigt met de Azure CLI. 

## <a name="resize-a-vm"></a>De grootte van een virtuele machine wijzigen
Als u het formaat van een vm wilt wijzigen, moet u de nieuwste [Azure CLI-installatie](/cli/azure/install-az-cli2) en aangemeld bij een Azure-account gebruiken met [az-aanmelding.](/cli/azure/reference-index)

1. Bekijk de lijst met beschikbare VM-formaten op het hardwarecluster waar de VM wordt gehost met [az vm list-vm-resize-options](/cli/azure/vm). In het volgende voorbeeld worden `myVM` VM-formaten `myResourceGroup` weergegeven voor de VM die is genoemd in het gebied resourcegroep:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Als de gewenste VM-grootte wordt weergegeven, wijzigt u het formaat van de VM met [het formaat van de AZ-vm](/cli/azure/vm). In het volgende voorbeeld `myVM` wordt `Standard_DS3_v2` het formaat van de vm met de naam grootte gewijzigd:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    De VM wordt tijdens dit proces opnieuw opgestart. Na de herstart worden uw bestaande besturingssysteem- en gegevensschijven opnieuw uitgevoerd. Alles op de tijdelijke schijf is verloren.

3. Als de gewenste VM-grootte niet wordt vermeld, moet u eerst de VM met [az vm deallocate](/cli/azure/vm). Met dit proces kan de virtuele machine worden aangepast aan elke beschikbare grootte die de regio ondersteunt en vervolgens wordt gestart. De volgende stappen deallocate, het formaat en `myVM` het formaat `myResourceGroup`van de VM met de naam in de resourcegroep met de naam:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Als u de VM deallocateert, worden ook dynamische IP-adressen die aan de VM zijn toegewezen, vrijgegeven. Het besturingssysteem en de gegevensschijven worden niet beïnvloed.

## <a name="next-steps"></a>Volgende stappen
Voer voor extra schaalbaarheid meerdere VM-exemplaren uit en schaal t.a.v. uit. Zie [Linux-machines automatisch schalen in een virtuele machineschaalset][scale-set]voor meer informatie. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
