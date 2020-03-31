---
title: Shell-scripts uitvoeren in een Linux-vm op Azure
description: In dit onderwerp wordt beschreven hoe u scripts uitvoert binnen een virtuele Azure Linux-machine met behulp van de functie Opdracht uitvoeren
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 21787854590d3ca0be2cbd6e9d167de33482c787
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72597886"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Shell-scripts uitvoeren in uw Linux-VM met Run Command

De functie Opdracht uitvoeren gebruikt de VM-agent (Virtual Machine) om shellscripts uit te voeren binnen een Azure Linux VM. U deze scripts gebruiken voor algemeen machine- of toepassingsbeheer. Ze kunnen u helpen om snel vm-toegang en netwerkproblemen te diagnosticeren en te verhelpen en de VM weer in goede staat te krijgen.

## <a name="benefits"></a>Voordelen

U hebt op meerdere manieren toegang tot uw virtuele machines. Run Command kan scripts op uw virtuele machines op afstand uitvoeren met behulp van de VM-agent. U gebruikt Opdracht uitvoeren via de Azure-portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)of [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) voor Linux VM's.

Deze mogelijkheid is handig in alle scenario's waarin u een script binnen een virtuele machine wilt uitvoeren. Het is een van de enige manieren om een virtuele machine op te lossen en te herstellen die de RDP- of SSH-poort niet open heeft vanwege onjuiste netwerk- of administratieve gebruikersconfiguratie.

## <a name="restrictions"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u Opdracht uitvoeren gebruikt:

* De uitvoer is beperkt tot de laatste 4.096 bytes.
* De minimale tijd om een script uit te voeren is ongeveer 20 seconden.
* Scripts worden standaard uitgevoerd als een verhoogde gebruiker op Linux.
* U één script tegelijk uitvoeren.
* Scripts die om informatie vragen (interactieve modus) worden niet ondersteund.
* Je een hardloopscript niet annuleren.
* De maximale tijd die een script kan uitvoeren is 90 minuten. Daarna zal het script een time-out krijgen.
* Uitgaande connectiviteit van de VM is vereist om de resultaten van het script te retourneren.

> [!NOTE]
> Om correct te kunnen functioneren, vereist Run Command connectiviteit (poort 443) met openbare IP-adressen van Azure. Als de extensie geen toegang heeft tot deze eindpunten, worden de scripts mogelijk uitgevoerd, maar worden de resultaten niet retourneren. Als u verkeer op de virtuele machine blokkeert, u [servicetags](../../virtual-network/security-overview.md#service-tags) gebruiken om `AzureCloud` verkeer naar openbare IP-adressen van Azure toe te staan met behulp van de tag.

## <a name="available-commands"></a>Beschikbare opdrachten

In deze tabel ziet u de lijst met opdrachten die beschikbaar zijn voor Linux-VM's. U de opdracht **RunShellScript** gebruiken om elk aangepast script uit te voeren dat u wilt. Wanneer u de Azure CLI of PowerShell gebruikt om een opdracht `--command-id` uit `-CommandId` te voeren, moet de waarde die u voor de parameter of parameter geeft, een van de volgende vermelde waarden zijn. Wanneer u een waarde opgeeft die geen beschikbare opdracht is, ontvangt u deze fout:

```error
The entity was not found in this Azure location
```

|**Naam**|**Beschrijving**|
|---|---|
|**RunShellScript**|Draait een Linux shell script.|
|**Ifconfig**| Krijgt de configuratie van alle netwerkinterfaces.|

## <a name="azure-cli"></a>Azure-CLI

In het volgende voorbeeld wordt de opdracht [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) gebruikt om een shellscript uit te voeren op een Azure Linux VM.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Als u opdrachten als een `sudo -u` andere gebruiker wilt uitvoeren, voert u dit in om een gebruikersaccount op te geven.

## <a name="azure-portal"></a>Azure Portal

Ga naar een vm in de [Azure-portal](https://portal.azure.com) en selecteer **Opdracht Uitvoeren** onder **BEWERKINGEN**. U ziet een lijst met de beschikbare opdrachten die op de VM moeten worden uitgevoerd.

![Lijst met opdrachten](./media/run-command/run-command-list.png)

Kies een opdracht die u wilt uitvoeren. Sommige opdrachten hebben mogelijk optionele of vereiste invoerparameters. Voor deze opdrachten worden de parameters weergegeven als tekstvelden om de invoerwaarden op te geven. Voor elke opdracht u het script bekijken dat wordt uitgevoerd door **het script van Weergave**uit te vouwen. **RunShellScript** is anders dan de andere opdrachten, omdat u hiermee uw eigen aangepaste script leveren.

> [!NOTE]
> De ingebouwde opdrachten zijn niet bewerkbaar.

Nadat u de opdracht hebt gekozen, selecteert u **Uitvoeren** om het script uit te voeren. Nadat het script is voltooid, worden de uitvoer en eventuele fouten in het uitvoervenster geretourneerd. In de volgende schermafbeelding ziet u een voorbeelduitvoer van het uitvoeren van de opdracht **ifconfig.**

![Opdrachtscriptuitvoer uitvoeren](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

In het volgende voorbeeld wordt de cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) gebruikt om een PowerShell-script uit te voeren op een Azure VM. De cmdlet verwacht dat het `-ScriptPath` script waarnaar in de parameter wordt verwezen lokaal is naar waar de cmdlet wordt uitgevoerd.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Toegang tot Opdracht uitvoeren beperken

Voor het aanbieden van de runopdrachten of `Microsoft.Compute/locations/runCommands/read` het weergeven van de details van een opdracht is de toestemming op abonnementsniveau vereist. De ingebouwde [Reader-rol](../../role-based-access-control/built-in-roles.md#reader) en hogere niveaus hebben deze toestemming.

Voor het uitvoeren `Microsoft.Compute/virtualMachines/runCommand/action` van een opdracht is de machtiging op abonnementsniveau vereist. De rol [Virtuele machinebijdrager](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en hogere niveaus hebben deze toestemming.

U een van de [ingebouwde rollen](../../role-based-access-control/built-in-roles.md) gebruiken of een [aangepaste rol](../../role-based-access-control/custom-roles.md) maken om Opdracht uitvoeren te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie [Scripts uitvoeren in uw Linux VM](run-scripts-in-vm.md)voor meer informatie over andere manieren om scripts en opdrachten op afstand uit te voeren in uw VM.
