---
title: Shell scripts uitvoeren in een virtuele Linux-machine in azure
description: In dit onderwerp wordt beschreven hoe u scripts kunt uitvoeren op een virtuele Azure Linux-machine met behulp van de opdracht uitvoeren
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6550b6e3f59ff7e6bac39dfc1abcf829256122d4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376360"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Shell scripts uitvoeren in uw Linux-VM met de opdracht uitvoeren

De opdracht run maakt gebruik van de VM-agent om shell scripts uit te voeren binnen een Azure Linux-VM. Deze scripts kunnen worden gebruikt voor algemeen machine-of toepassings beheer en kunnen worden gebruikt om snel de toegang tot en het netwerk op te sporen en te herstellen en om de VM terug te brengen naar een goede status.

## <a name="benefits"></a>Voordelen

Er zijn meerdere opties die kunnen worden gebruikt om toegang te krijgen tot uw virtuele machines. Met de opdracht uitvoeren kunt u op afstand scripts uitvoeren op uw virtuele machines met behulp van de VM-agent. De opdracht uitvoeren kan worden gebruikt via de Azure Portal, [rest API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)of [Azure cli](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) voor Linux vm's.

Deze mogelijkheid is handig in alle scenario's waarin u een script wilt uitvoeren binnen een virtuele machine en is een van de enige manieren om problemen op te lossen en te herstellen van een virtuele machines waarvoor geen RDP-of SSH-poort is geopend vanwege een onjuiste netwerk-of gebruiker met beheerders rechten configuratie.

## <a name="restrictions"></a>Beperkingen

Hier volgt een lijst met de beperkingen die aanwezig zijn bij het gebruik van de opdracht run.

* De uitvoer is beperkt tot de laatste 4096 bytes
* De minimale tijd om een script ongeveer 20 seconden uit te voeren
* Scripts worden standaard uitgevoerd als verhoogde gebruiker op Linux
* Eén script tegelijk kan worden uitgevoerd
* Scripts die vragen om informatie (interactieve modus) worden niet ondersteund.
* U kunt een actief script niet annuleren
* De maximale tijd dat een script kan worden uitgevoerd, is 90 minuten, waarna er een time-out optreedt
* Uitgaande verbinding van de virtuele machine is vereist om de resultaten van het script te retour neren.

> [!NOTE]
> Voor een juiste werking van de opdracht uitvoeren is connectiviteit (poort 443) vereist voor open bare IP-adressen van Azure. Als de extensie geen toegang tot deze eind punten heeft, kunnen de scripts worden uitgevoerd, maar niet de resultaten retour neren. Als u verkeer op de virtuele machine blokkeert, kunt u [service Tags](../../virtual-network/security-overview.md#service-tags) gebruiken om verkeer naar open bare IP-adressen van Azure toe te staan met behulp van de code `AzureCloud`.

## <a name="available-commands"></a>Beschik bare opdrachten

In deze tabel ziet u de lijst met opdrachten die beschikbaar zijn voor Linux-Vm's. De opdracht **RunShellScript** kan worden gebruikt om elk gewenst aangepast script uit te voeren. Wanneer u Azure CLI of Power shell gebruikt om een opdracht uit te voeren, moet de waarde die u opgeeft voor de para meter `--command-id` of `-CommandId` een van de onderstaande waarden zijn. Wanneer u een waarde opgeeft die geen beschik bare opdracht is, wordt het fout bericht weer gegeven.

```error
The entity was not found in this Azure location
```

|**Naam**|**Beschrijving**|
|---|---|
|**RunShellScript**|Voert een Linux-shell script uit.|
|**ifconfig**| De configuratie van alle netwerk interfaces ophalen.|

## <a name="azure-cli"></a>Azure CLI

Hier volgt een voor beeld van het gebruik van de opdracht [AZ VM Run-Command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) om een shell script uit te voeren op een virtuele Azure Linux-machine.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Als u opdrachten wilt uitvoeren als een andere gebruiker, kunt u `sudo -u` gebruiken om een gebruikers account op te geven dat moet worden gebruikt.

## <a name="azure-portal"></a>Azure Portal

Navigeer naar een virtuele machine in [Azure](https://portal.azure.com) en selecteer **opdracht uitvoeren** onder **bewerkingen**. Er wordt een lijst weer gegeven met de beschik bare opdrachten die op de virtuele machine kunnen worden uitgevoerd.

![Opdracht lijst uitvoeren](./media/run-command/run-command-list.png)

Kies een opdracht die moet worden uitgevoerd. Sommige van de opdrachten hebben mogelijk optionele of vereiste invoer parameters. Voor deze opdrachten worden de para meters als tekst velden weer gegeven, zodat u de invoer waarden kunt opgeven. Voor elke opdracht kunt u het script bekijken dat wordt uitgevoerd door een uitgebreid **weergave script**uit te voeren. **RunShellScript** wijkt af van de andere opdrachten, omdat u hiermee uw eigen aangepaste script kunt opgeven.

> [!NOTE]
> De ingebouwde opdrachten kunnen niet worden bewerkt.

Zodra de opdracht is gekozen, klikt u op **uitvoeren** om het script uit te voeren. Het script wordt uitgevoerd en wanneer dit is voltooid, retourneert de uitvoer en eventuele fouten in het uitvoer venster. De volgende scherm afbeelding toont een voorbeeld uitvoer van het uitvoeren van de **ifconfig** -opdracht.

![Uitvoer van opdracht script uitvoeren](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

Hier volgt een voor beeld van het gebruik van de cmdlet [invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) om een Power shell-script uit te voeren op een virtuele machine van Azure. De cmdlet verwacht dat het script waarnaar wordt verwezen in de para meter `-ScriptPath`, lokaal is waar de cmdlet wordt uitgevoerd.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Toegang beperken tot de opdracht uitvoeren

Voor het weer geven van de uitvoerings opdrachten of het weer geven van de details van een opdracht is de `Microsoft.Compute/locations/runCommands/read`-machtiging op abonnements niveau vereist, die de ingebouwde rol van [lezer](../../role-based-access-control/built-in-roles.md#reader) en hoger hebben.

Voor het uitvoeren van een opdracht is de machtiging @no__t 0 op abonnements niveau vereist, die de rol van [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en hoger hebben.

U kunt een van de [ingebouwde](../../role-based-access-control/built-in-roles.md) rollen gebruiken of een [aangepaste](../../role-based-access-control/custom-roles.md) rol maken voor het gebruik van de opdracht uitvoeren.

## <a name="next-steps"></a>Volgende stappen

Zie [scripts uitvoeren in uw virtuele Linux-machine](run-scripts-in-vm.md) voor meer informatie over andere manieren om scripts en opdrachten op afstand uit te voeren in uw VM.
