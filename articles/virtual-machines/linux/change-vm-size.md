---
title: Het formaat van een virtuele Linux-machine wijzigen met de Azure CLI
description: U kunt een virtuele Linux-machine omhoog of omlaag schalen door de VM-grootte te wijzigen.
author: mikewasson
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2017
ms.author: mwasson
ms.openlocfilehash: 20e7db80b55347c4a4a76b7c95d4d8bec368abda
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969263"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Het formaat van een virtuele Linux-machine wijzigen met behulp van Azure CLI 

Nadat u een virtuele machine (VM) hebt ingericht, kunt u de VM omhoog of omlaag schalen door de [VM-grootte][vm-sizes]te wijzigen. In sommige gevallen moet u eerst de toewijzing van de VM ongedaan maken. U moet de toewijzing van de virtuele machine ongedaan maken als de gewenste grootte niet beschikbaar is op het hardwareplatform dat als host fungeert voor de virtuele machine. In dit artikel wordt beschreven hoe u het formaat van een virtuele Linux-machine kunt wijzigen met de Azure CLI. 

## <a name="resize-a-vm"></a>De grootte van een VM wijzigen
Als u de grootte van een virtuele machine wilt wijzigen, moet u de nieuwste [Azure cli](/cli/azure/install-az-cli2) installeren en u aanmelden bij een Azure-account met [AZ login](/cli/azure/reference-index).

1. Bekijk de lijst met beschik bare VM-grootten op het hardware-cluster waarop de virtuele machine wordt gehost met [AZ VM List-VM-resize-Options](/cli/azure/vm). In het volgende voor beeld wordt een overzicht gegeven van de VM-grootten voor de VM met de naam `myVM` in de resource groep `myResourceGroup` regio:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Als de gewenste VM-grootte wordt weer gegeven, wijzigt u het formaat van de virtuele machine met [AZ VM resize](/cli/azure/vm). In het volgende voor beeld wordt de grootte van de virtuele machine met de naam `myVM` gewijzigd in de `Standard_DS3_v2` grootte:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    De virtuele machine wordt opnieuw opgestart tijdens dit proces. Na het opnieuw opstarten worden uw bestaande besturings systeem en de gegevens schijven opnieuw toegewezen. Alles op de tijdelijke schijf gaat verloren.

3. Als de gewenste VM-grootte niet wordt weer gegeven, moet u eerst de toewijzing van de virtuele machine met [AZ VM disallocate](/cli/azure/vm)ongedaan maken. Met dit proces kan de VM vervolgens worden verg root of verkleind tot elke beschik bare grootte die beschikbaar is voor de regio en vervolgens wordt gestart. Met de volgende stappen wordt de toewijzing van de virtuele machine in de resource groep met de naam `myResourceGroup`ongedaan gemaakt, het formaat ervan gewijzigd en wordt de `myVM` VM vervolgens gestart:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Als u de toewijzing van de virtuele machine ongedaan hebt, worden er ook dynamische IP-adressen vrijgegeven die aan de virtuele machine Het besturings systeem en de gegevens schijven worden niet beïnvloed.

## <a name="next-steps"></a>Volgende stappen
Voor extra schaal baarheid voert u meerdere VM-exemplaren uit en uitschalen. Zie [Linux-machines automatisch schalen in een schaalset voor virtuele machines][scale-set]voor meer informatie. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
