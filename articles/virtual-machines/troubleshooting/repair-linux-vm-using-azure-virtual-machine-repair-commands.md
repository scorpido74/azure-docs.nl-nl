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
ms.openlocfilehash: a7357ef3b0151096746e37bddd235f0db53baed7
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85444808"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Een Linux-VM herstellen met de reparatieopdrachten van Azure Virtual Machine

Als op de virtuele Linux-machine (VM) in azure een opstart-of schijf fout optreedt, moet u mogelijk de schijf zelf beperken. Een veelvoorkomend voor beeld hiervan is een mislukte toepassings update waarmee wordt voor komen dat de virtuele machine kan worden opgestart. In dit artikel wordt beschreven hoe u de herstel opdrachten van de virtuele machine van Azure kunt gebruiken om de schijf te verbinden met een andere virtuele Linux-machine om eventuele fouten op te lossen en vervolgens uw oorspronkelijke VM opnieuw op te bouwen.

> [!IMPORTANT]
> * De scripts in dit artikel zijn alleen van toepassing op de virtuele machines die gebruikmaken van [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
> * De uitgaande verbinding van de virtuele machine (poort 443) is vereist om het script uit te voeren.
> * Er kan slechts één script tegelijk worden uitgevoerd.
> * Een actief script kan niet worden geannuleerd.
> * De maximale tijd dat een script kan worden uitgevoerd, is 90 minuten, waarna er een time-out optreedt.
> * Voor Vm's met Azure Disk Encryption worden alleen beheerde schijven ondersteund die zijn versleuteld met eenmalige versleuteling (met of zonder KEK).

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

   Als u de Cloud Shell wilt openen, selecteert u **deze** in de rechter bovenhoek van een code blok. U kunt Cloud Shell ook openen in een afzonderlijk browser tabblad door naar te gaan [https://shell.azure.com](https://shell.azure.com) .

   Selecteer **kopiëren** om de blokken code te kopiëren en plak de code in het Cloud shell en selecteer **Enter** om het programma uit te voeren.

   Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u voor deze snelstart versie 2.0.30 of hoger van Azure CLI nodig. Voer ``az --version`` uit om de versie te bekijken. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).
   
   Als u zich moet aanmelden bij Cloud Shell met een ander account dan u momenteel bent aangemeld bij de Azure-Portal, kunt u de ``az login`` [referenties van AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)gebruiken.  Als u wilt scha kelen tussen de abonnementen die zijn gekoppeld aan uw account, kunt u ``az account set --subscription`` [AZ account set reference](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set)gebruiken.

2. Als dit de eerste keer is dat u de `az vm repair` opdrachten gebruikt, voegt u de extensie VM-herstel cli toe.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Als u de opdrachten eerder hebt gebruikt `az vm repair` , moet u alle updates Toep assen op de extensie voor VM-herstel.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Voer `az vm repair create` uit. Met deze opdracht maakt u een kopie van de besturingssysteem schijf voor de niet-functionele VM, maakt u een herstel-VM in een nieuwe resource groep en koppelt u de kopie van de besturingssysteem schijf.  De herstel-VM heeft dezelfde grootte en regio als de niet-functionele VM die is opgegeven. De resource groep en de VM-naam die in alle stappen worden gebruikt, zijn voor de niet-functionele VM. Als uw virtuele machine wordt gebruikt Azure Disk Encryption wordt met de opdracht geprobeerd de versleutelde schijf te ontgrendelen, zodat deze toegankelijk is wanneer deze wordt gekoppeld aan de reparatie-VM.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Voer de benodigde stappen uit op de gemaakte herstel-VM en ga vervolgens verder met stap 5.

5. Voer `az vm repair restore` uit. Met deze opdracht wordt de gerepareerde besturingssysteem schijf vervangen door de oorspronkelijke besturingssysteem schijf van de virtuele machine. De resource groep en de VM-naam die hier worden gebruikt voor de niet-functionele VM die u in stap 3 gebruikt.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Diagnostische gegevens over opstarten controleren en inschakelen

In het volgende voor beeld wordt de diagnostische uitbrei ding ingeschakeld op de virtuele machine ``myVMDeployed`` met de naam in de resource groep met de naam ``myResourceGroup`` :

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Volgende stappen

* Zie [problemen met RDP-verbindingen met een virtuele machine van Azure oplossen](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)als u problemen ondervindt bij het maken van verbinding met uw VM.
* Zie problemen met [toepassings connectiviteit oplossen op virtuele machines in azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection)voor problemen met het openen van toepassingen die op uw virtuele machine worden uitgevoerd.
* Zie [Azure Resource Manager Overview](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)voor meer informatie over het gebruik van Resource Manager.
