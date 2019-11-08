---
title: Een Linux-VM herstellen met behulp van de opdrachten voor het herstellen van virtuele Azure-machines | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de herstel opdrachten van de virtuele machine van Azure kunt gebruiken om de schijf te verbinden met een andere virtuele Linux-machine om eventuele fouten op te lossen en vervolgens uw oorspronkelijke VM opnieuw op te bouwen.
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
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796205"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Een Linux-VM herstellen met de reparatieopdrachten van Azure Virtual Machine

Als op de virtuele Linux-machine (VM) in azure een opstart-of schijf fout optreedt, moet u mogelijk de schijf zelf beperken. Een veelvoorkomend voor beeld hiervan is een mislukte toepassings update waarmee wordt voor komen dat de virtuele machine kan worden opgestart. In dit artikel wordt beschreven hoe u de herstel opdrachten van de virtuele machine van Azure kunt gebruiken om de schijf te verbinden met een andere virtuele Linux-machine om eventuele fouten op te lossen en vervolgens uw oorspronkelijke VM opnieuw op te bouwen.

> [!IMPORTANT]
> De scripts in dit artikel zijn alleen van toepassing op de virtuele machines die gebruikmaken van [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Overzicht van het reparatie proces

U kunt nu de opdrachten voor het herstellen van virtuele machines van Azure gebruiken om de besturingssysteem schijf voor een virtuele machine te wijzigen. u hoeft de virtuele machine niet meer te verwijderen en opnieuw te maken.

Volg deze stappen om het VM-probleem op te lossen:

1. Azure Cloud Shell starten
2. Uitvoeren AZ extension add/update
3. Uitvoeren AZ VM Repair Create
4. Stappen voor risico beperking uitvoeren
5. Uitvoeren AZ VM Repair Restore

Zie [AZ VM Repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair)(Engelstalig) voor aanvullende documentatie en instructies.

## <a name="repair-process-example"></a>Voor beeld van reparatie proces

1. Azure Cloud Shell starten

   Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. Het bevat algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd en geconfigureerd voor gebruik met uw account.

   Als u de Cloud Shell wilt openen, selecteert u **deze** in de rechter bovenhoek van een code blok. U kunt Cloud Shell ook openen in een afzonderlijk browser tabblad door naar [https://shell.azure.com](https://shell.azure.com)te gaan.

   Selecteer **kopiëren** om de blokken code te kopiëren en plak de code in het Cloud shell en selecteer **Enter** om het programma uit te voeren.

   Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u voor deze snelstart versie 2.0.30 of hoger van Azure CLI nodig. Voer ``az --version`` uit om de versie te bekijken. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Als dit de eerste keer is dat u de `az vm repair`-opdrachten gebruikt, voegt u de extensie VM-Repair CLI toe.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Als u eerder de `az vm repair`-opdrachten hebt gebruikt, past u alle updates toe op de extensie voor VM-herstel.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Voer `az vm repair create` uit. Met deze opdracht maakt u een kopie van de besturingssysteem schijf voor de niet-functionele VM, maakt u een herstel-VM en koppelt u de schijf.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Voer de benodigde stappen uit op de gemaakte herstel-VM en ga vervolgens verder met stap 5.

5. Voer `az vm repair restore` uit. Met deze opdracht wordt de gerepareerde besturingssysteem schijf vervangen door de oorspronkelijke besturingssysteem schijf van de virtuele machine.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Diagnostische gegevens over opstarten controleren en inschakelen

In het volgende voor beeld wordt de diagnostische uitbrei ding ingeschakeld op de virtuele machine met de naam ``myVMDeployed`` in de resource groep met de naam ``myResourceGroup``:

Azure-CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Volgende stappen

* Zie [problemen met RDP-verbindingen met een virtuele machine van Azure oplossen](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)als u problemen ondervindt bij het maken van verbinding met uw VM.
* Zie problemen met [toepassings connectiviteit oplossen op virtuele machines in azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection)voor problemen met het openen van toepassingen die op uw virtuele machine worden uitgevoerd.
* Zie [Azure Resource Manager Overview](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)voor meer informatie over het gebruik van Resource Manager.
