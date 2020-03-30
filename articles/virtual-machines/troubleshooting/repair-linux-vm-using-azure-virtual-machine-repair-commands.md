---
title: Een Linux-vm herstellen met behulp van de reparatieopdrachten van Azure Virtual Machine | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u azure virtual machine-reparatieopdrachten gebruikt om de schijf aan te sluiten op een andere Linux-vm om eventuele fouten op te lossen en vervolgens uw oorspronkelijke vm opnieuw op te bouwen.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 49fdfde402938ce8d0ee1b141a47e68c99c502e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73796205"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Een Linux-VM herstellen met de reparatieopdrachten van Azure Virtual Machine

Als uw Virtuele Linux-machine (VM) in Azure een opstart- of schijffout tegenkomt, moet u mogelijk mitigatie uitvoeren op de schijf zelf. Een veelvoorkomend voorbeeld is een mislukte toepassingsupdate die voorkomt dat de VM kan opstarten. In dit artikel wordt beschreven hoe u azure virtual machine-reparatieopdrachten gebruikt om de schijf aan te sluiten op een andere Linux-vm om eventuele fouten op te lossen en vervolgens uw oorspronkelijke vm opnieuw op te bouwen.

> [!IMPORTANT]
> De scripts in dit artikel zijn alleen van toepassing op de VM's die [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)gebruiken.

## <a name="repair-process-overview"></a>Overzicht van reparatieprocessen

U nu azure virtual machine-reparatieopdrachten gebruiken om de OS-schijf voor een virtuele machine te wijzigen en u hoeft de VM niet langer te verwijderen en opnieuw te maken.

Voer de volgende stappen uit om het VM-probleem op te lossen:

1. Azure Cloud Shell starten
2. Add/update az-extensie uitvoeren
3. Az vm-reparatie maken uitvoeren
4. Beperkende stappen uitvoeren
5. Az vm reparatie herstellen uitvoeren

Zie [az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair)voor aanvullende documentatie en instructies.

## <a name="repair-process-example"></a>Voorbeeld van reparatieproces

1. Azure Cloud Shell starten

   Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. Het bevat algemene Azure-hulpprogramma's die vooraf zijn geïnstalleerd en geconfigureerd om te gebruiken met uw account.

   Als u de Cloud Shell wilt openen, selecteert **u Probeer deze** in de rechterbovenhoek van een codeblok. U Cloud Shell ook openen op [https://shell.azure.com](https://shell.azure.com)een apart browsertabblad door naar.

   Selecteer **Kopiëren** om de codeblokken te kopiëren, plak de code vervolgens in de Cloud Shell en selecteer **Enter** om deze uit te voeren.

   Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u voor deze snelstart versie 2.0.30 of hoger van Azure CLI nodig. Voer ``az --version`` uit om de versie te bekijken. Zie [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)installeren als u uw Azure CLI moet installeren of upgraden.

2. Als dit de eerste keer `az vm repair` is dat u de opdrachten hebt gebruikt, voegt u de CLI-extensie vm-reparatie toe.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Als u de `az vm repair` opdrachten eerder hebt gebruikt, past u eventuele updates toe op de vm-reparatieextensie.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Voer `az vm repair create` uit. Met deze opdracht wordt een kopie van de OS-schijf gemaakt voor de niet-functionele vm, wordt een reparatie-vm gemaakt en wordt de schijf bevestigd.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Voer alle benodigde mitigatiestappen uit op de gemaakte reparatie-VM en ga vervolgens verder met stap 5.

5. Voer `az vm repair restore` uit. Met deze opdracht wordt de gerepareerde osschijf verwisseld met de oorspronkelijke osschijf van de VM.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Opstartdiagnose verifiëren en inschakelen

In het volgende voorbeeld wordt de ``myVMDeployed`` diagnostische extensie ``myResourceGroup``op de VM ingeschakeld die is genoemd in de resourcegroep met de naam :

Azure-CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Volgende stappen

* Zie Problemen [met RDP-verbindingen met een Azure Virtual Machine](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)als u problemen ondervindt bij het maken van verbinding met uw vm.
* Zie Problemen met [de toepassingsconnectiviteit oplossen op virtuele machines in Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection)voor problemen met de toegang tot toepassingen die op uw vm worden uitgevoerd.
* Zie overzicht van Azure Resource Manager voor meer informatie over het gebruik van Resource [Manager.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
