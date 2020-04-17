---
title: Overzicht van Azure Virtual Machine Agent
description: Overzicht van Azure Virtual Machine Agent
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: f29a20ddeb93ec3d4aa98bbcb36f50456b543667
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452567"
---
# <a name="azure-virtual-machine-agent-overview"></a>Overzicht van Azure Virtual Machine Agent
De Microsoft Azure Virtual Machine Agent (VM Agent) is een veilig, lichtgewicht proces dat de virtuele machine (VM) interactie met de Azure Fabric Controller beheert. De VM-agent heeft een primaire rol bij het in- en uitvoeren van Azure-extensies voor virtuele machines. VM-extensies maken post-deployment configuratie van VM mogelijk, zoals het installeren en configureren van software. VM-extensies maken ook herstelfuncties mogelijk, zoals het opnieuw instellen van het beheerderswachtwoord van een virtuele machine. Zonder de Azure VM-agent kunnen VM-extensies niet worden uitgevoerd.

In dit artikel wordt de installatie en detectie van de Azure Virtual Machine Agent beschreven.

## <a name="install-the-vm-agent"></a>De VM-agent installeren

### <a name="azure-marketplace-image"></a>Azure Marketplace-afbeelding

De Azure VM-agent is standaard geïnstalleerd op elke Windows VM die is geïmplementeerd vanuit een Azure Marketplace-afbeelding. Wanneer u een Azure Marketplace-afbeelding implementeert vanuit de portal, PowerShell, Command Line Interface of een Azure Resource Manager-sjabloon, wordt ook de Azure VM Agent geïnstalleerd.

Het Windows Guest Agent-pakket is opgesplitst in twee delen:

- Provisioning Agent (PA)
- Windows Guest Agent (WinGA)

Als u een VM wilt opstarten, moet u de PA op de VM hebben geïnstalleerd, maar de WinGA hoeft niet te worden geïnstalleerd. Op VM-implementatietijd u ervoor kiezen de WinGA niet te installeren. In het volgende voorbeeld ziet u hoe u de optie *provisionVmAgent* selecteert met een azure resourcemanager-sjabloon:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Als de Agents niet zijn geïnstalleerd, u bepaalde Azure-services, zoals Azure Backup of Azure Security, niet gebruiken. Voor deze services moet een extensie worden geïnstalleerd. Als u een VM zonder de WinGA hebt geïmplementeerd, u de nieuwste versie van de agent later installeren.

### <a name="manual-installation"></a>Handmatige installatie
De Windows VM-agent kan handmatig worden geïnstalleerd met een Windows-installatiepakket. Handmatige installatie kan nodig zijn wanneer u een aangepaste VM-afbeelding maakt die is geïmplementeerd in Azure. Als u de Windows VM-agent handmatig wilt installeren, [downloadt u het installatieprogramma VM-agent](https://go.microsoft.com/fwlink/?LinkID=394789). De VM-agent wordt ondersteund op Windows Server 2008 R2 en hoger.

> [!NOTE]
> Het is belangrijk om de optie AllowExtensionOperations bij te werken nadat u de VMAgent handmatig hebt geïnstalleerd op een VM die is geïmplementeerd vanuit de afbeelding zonder dat ProvisionVMAgent is ingeschakeld.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Vereisten
- De Windows VM-agent moet ten minste Windows Server 2008 R2 (64-bits) worden uitgevoerd, met de .Net Framework 4.0. Zie [Minimale versieondersteuning voor virtuele machineagents in Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- Zorg ervoor dat uw vm toegang heeft tot IP-adres 168.63.129.16. Zie Voor meer informatie [Wat is IP-adres 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

## <a name="detect-the-vm-agent"></a>De VM-agent detecteren

### <a name="powershell"></a>PowerShell

De PowerShell-module Azure Resource Manager kan worden gebruikt om informatie over Azure VM's op te halen. Als u informatie over een vm wilt bekijken, zoals de inrichtingsstatus voor de Azure VM-agent, gebruikt u [Get-AzVM:](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)

```powershell
Get-AzVM
```

In de volgende verkorte voorbeelduitvoer ziet u de eigenschap *ProvisionVMAgent* die is genest in *OSProfile*. Deze eigenschap kan worden gebruikt om te bepalen of de VM-agent is geïmplementeerd op de VM:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Het volgende script kan worden gebruikt om een beknopte lijst met VM-namen en de status van de VM-agent terug te sturen:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Handmatige detectie

Wanneer u bent aangemeld bij een Windows-vm, kan Taakbeheer worden gebruikt om lopende processen te onderzoeken. Als u wilt controleren of de Azure VM-agent de Azure VM-agent opent, opent u Taakbeheer, klikt u op het tabblad *Details* en zoekt u naar de procesnaam **WindowsAzureGuestAgent.exe**. De aanwezigheid van dit proces geeft aan dat de VM-agent is geïnstalleerd.


## <a name="upgrade-the-vm-agent"></a>De VM-agent bijwerken
De Azure VM-agent voor Windows wordt automatisch geüpgraded. Als nieuwe VM's worden geïmplementeerd in Azure, ontvangen ze de nieuwste VM-agent op VM-provisiontime. Aangepaste VM-afbeeldingen moeten handmatig worden bijgewerkt om de nieuwe VM-agent op te nemen tijdens het maken van afbeeldingen.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Verzameling automatische logboeken van Windows Guest Agent
Windows Guest Agent heeft een functie om automatisch een aantal logboeken te verzamelen. Deze functie wordt gecontroller door het CollectGuestLogs.exe-proces. Het bestaat voor zowel PaaS Cloud Services en IaaS Virtual Machines en het doel is om snel & automatisch verzamelen van een aantal diagnostische logs van een VM - zodat ze kunnen worden gebruikt voor offline analyse. De verzamelde logboeken zijn gebeurtenislogboeken, OS-logboeken, Azure-logboeken en enkele registersleutels. Het produceert een ZIP-bestand dat wordt overgedragen aan de host van de VM. Dit ZIP-bestand kan vervolgens worden bekeken door Engineering Teams en Support professionals om problemen te onderzoeken op verzoek van de klant die eigenaar is van de VM.

## <a name="next-steps"></a>Volgende stappen
Zie overzicht van [Azure-functies voor virtuele machine-extensies en -functies](overview.md)voor meer informatie over VM-extensies.
