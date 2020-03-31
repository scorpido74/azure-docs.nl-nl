---
title: PowerShell-scripts uitvoeren in een Windows-vm in Azure
description: In dit onderwerp wordt beschreven hoe u PowerShell-scripts uitvoert in een virtuele Azure Windows-machine met de functie Opdracht uitvoeren
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fa7f72989d47499127714eddfa6b5e98aa80178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749221"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>PowerShell-scripts uitvoeren in uw Windows-vm met Opdracht uitvoeren

De functie Opdracht uitvoeren gebruikt de VM-agent (Virtual Machine) om PowerShell-scripts uit te voeren binnen een Azure Windows VM. U deze scripts gebruiken voor algemeen machine- of toepassingsbeheer. Ze kunnen u helpen om snel vm-toegang en netwerkproblemen te diagnosticeren en te verhelpen en de VM weer in goede staat te krijgen.

 

## <a name="benefits"></a>Voordelen

U hebt op meerdere manieren toegang tot uw virtuele machines. Run Command kan scripts op uw virtuele machines op afstand uitvoeren met behulp van de VM-agent. U gebruikt Opdracht uitvoeren via de Azure-portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)of [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) voor Windows VM's.

Deze mogelijkheid is handig in alle scenario's waarin u een script binnen een virtuele machine wilt uitvoeren. Het is een van de enige manieren om een virtuele machine op te lossen en te herstellen die de RDP- of SSH-poort niet open heeft vanwege onjuiste netwerk- of administratieve gebruikersconfiguratie.

## <a name="restrictions"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u Opdracht uitvoeren gebruikt:

* De uitvoer is beperkt tot de laatste 4.096 bytes.
* De minimale tijd om een script uit te voeren is ongeveer 20 seconden.
* Scripts worden uitgevoerd als systeem op Windows.
* Eén script tegelijk kan worden uitgevoerd.
* Scripts die om informatie vragen (interactieve modus) worden niet ondersteund.
* Je een hardloopscript niet annuleren.
* De maximale tijd die een script kan uitvoeren is 90 minuten. Daarna zal het een time-out.
* Uitgaande connectiviteit van de VM is vereist om de resultaten van het script te retourneren.

> [!NOTE]
> Om correct te kunnen functioneren, vereist Run Command connectiviteit (poort 443) met openbare IP-adressen van Azure. Als de extensie geen toegang heeft tot deze eindpunten, worden de scripts mogelijk uitgevoerd, maar worden de resultaten niet retourneren. Als u verkeer op de virtuele machine blokkeert, u [servicetags](../../virtual-network/security-overview.md#service-tags) gebruiken om `AzureCloud` verkeer naar openbare IP-adressen van Azure toe te staan met behulp van de tag.

## <a name="available-commands"></a>Beschikbare opdrachten

In deze tabel ziet u de lijst met opdrachten die beschikbaar zijn voor Windows VM's. U de opdracht **RunPowerShellScript** gebruiken om elk aangepast script uit te voeren dat u wilt. Wanneer u de Azure CLI of PowerShell gebruikt om een opdracht `--command-id` uit `-CommandId` te voeren, moet de waarde die u voor de parameter of parameter geeft, een van de volgende vermelde waarden zijn. Wanneer u een waarde opgeeft die geen beschikbare opdracht is, ontvangt u deze fout:

```error
The entity was not found in this Azure location
```

|**Naam**|**Beschrijving**|
|---|---|
|**RunPowerShellScript**|Met een PowerShell-script wordt een PowerShell-script uitgevoerd.|
|**RemotePS inschakelen**|Hiermee configureert u de machine om externe PowerShell in te schakelen.|
|**EnableAdminAccount**|Hiermee wordt gecontroleerd of het lokale beheerdersaccount is uitgeschakeld en zo ja, en zo ja, dat dit mogelijk maakt.|
|**Ipconfig**| Toont gedetailleerde informatie voor het IP-adres, subnetmasker en standaardgateway voor elke adapter die is gekoppeld aan TCP/IP.|
|**RDP-instellingen**|Controleert registerinstellingen en domeinbeleidsinstellingen. Stelt beleidsacties voor als de machine deel uitmaakt van een domein of de instellingen wijzigt in standaardwaarden.|
|**Rdpcert opnieuw instellen**|Hiermee verwijdert u het SSL-certificaat dat is gekoppeld aan de RDP-listener en wordt de beveiliging van de RDP-listener standaard hersteld. Gebruik dit script als u problemen ziet met het certificaat.|
|**SetRDPPort**|Hiermee stelt u het standaard- of door de gebruiker opgegeven poortnummer in voor Extern bureaublad-verbindingen. Hiermee kunnen firewallregels voor binnenkomende toegang tot de poort worden geopend.|

## <a name="azure-cli"></a>Azure-CLI

In het volgende voorbeeld wordt de opdracht [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) gebruikt om een shellscript uit te voeren op een Azure Windows VM.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure Portal

Ga naar een vm in de [Azure-portal](https://portal.azure.com) en selecteer **Opdracht Uitvoeren** onder **BEWERKINGEN**. U ziet een lijst met de beschikbare opdrachten die op de VM moeten worden uitgevoerd.

![Lijst met opdrachten](./media/run-command/run-command-list.png)

Kies een opdracht die u wilt uitvoeren. Sommige opdrachten hebben mogelijk optionele of vereiste invoerparameters. Voor deze opdrachten worden de parameters weergegeven als tekstvelden om de invoerwaarden op te geven. Voor elke opdracht u het script bekijken dat wordt uitgevoerd door **het script van Weergave**uit te vouwen. **RunPowerShellScript** is anders dan de andere opdrachten, omdat u hiermee uw eigen aangepaste script leveren.

> [!NOTE]
> De ingebouwde opdrachten zijn niet bewerkbaar.

Nadat u de opdracht hebt gekozen, selecteert u **Uitvoeren** om het script uit te voeren. Nadat het script is voltooid, worden de uitvoer en eventuele fouten in het uitvoervenster geretourneerd. In de volgende schermafbeelding wordt een voorbeelduitvoer weergegeven van het uitvoeren van de opdracht **RDPSettings.**

![Opdrachtscriptuitvoer uitvoeren](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

In het volgende voorbeeld wordt de cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) gebruikt om een PowerShell-script uit te voeren op een Azure VM. De cmdlet verwacht dat het `-ScriptPath` script waarnaar in de parameter wordt verwezen lokaal is naar waar de cmdlet wordt uitgevoerd.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Toegang tot Opdracht uitvoeren beperken

Voor het aanbieden van de runopdrachten of `Microsoft.Compute/locations/runCommands/read` het weergeven van de details van een opdracht is de toestemming op abonnementsniveau vereist. De ingebouwde [Reader-rol](../../role-based-access-control/built-in-roles.md#reader) en hogere niveaus hebben deze toestemming.

Voor het uitvoeren `Microsoft.Compute/virtualMachines/runCommand/action` van een opdracht is de machtiging op abonnementsniveau vereist. De rol [Virtuele machinebijdrager](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en hogere niveaus hebben deze toestemming.

U een van de [ingebouwde rollen](../../role-based-access-control/built-in-roles.md) gebruiken of een [aangepaste rol](../../role-based-access-control/custom-roles.md) maken om Opdracht uitvoeren te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie [Scripts uitvoeren in uw Windows VM](run-scripts-in-vm.md)voor meer informatie over andere manieren om scripts en opdrachten op afstand uit te voeren in uw VM.
