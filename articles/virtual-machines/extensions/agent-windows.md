---
title: Overzicht van Azure virtual machine agent | Microsoft Docs
description: Overzicht van de agent voor virtuele Azure-machines
services: virtual-machines-windows
documentationcenter: virtual-machines
author: roiyz-msft
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: roiyz
ms.openlocfilehash: c3b7b0588e6c1446203f7bb4a3ec7f168f08988f
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637500"
---
# <a name="azure-virtual-machine-agent-overview"></a>Overzicht van de agent voor virtuele Azure-machines
De Microsoft Azure-agent van de virtuele machine (VM-agent) is een veilig, licht gewicht proces dat interactie van de virtuele machine (VM) beheert met de Azure Fabric-controller. De VM-agent heeft een primaire rol bij het inschakelen en uitvoeren van extensies van virtuele Azure-machines. VM-extensies maken de configuratie van de na de implementatie van de VM mogelijk, zoals het installeren en configureren van software. VM-extensies bieden ook herstel functies, zoals het opnieuw instellen van het beheerders wachtwoord van een virtuele machine. Zonder de VM-agent van Azure kunnen VM-extensies niet worden uitgevoerd.

In dit artikel wordt de installatie, detectie en verwijdering van de Azure virtual machine-agent beschreven.

## <a name="install-the-vm-agent"></a>De VM-agent installeren

### <a name="azure-marketplace-image"></a>Azure Marketplace-installatie kopie

De Azure VM-agent wordt standaard geïnstalleerd op een virtuele Windows-machine die is geïmplementeerd vanuit een Azure Marketplace-installatie kopie. Wanneer u een installatie kopie van Azure Marketplace implementeert vanuit de portal, Power shell, de opdracht regel interface of een Azure Resource Manager sjabloon, wordt de Azure VM-agent ook geïnstalleerd.

Het pakket voor de Windows-gast agent is onderverdeeld in twee delen:

- Inrichtings agent (PA)
- Windows-gast agent (Vleugela)

Als u een virtuele machine wilt opstarten, moet u de PA geïnstalleerd hebben op de virtuele machine. de vleugels hoeven echter niet te worden geïnstalleerd. Op de implementatie tijd van de VM kunt u de Vleugela niet installeren. In het volgende voor beeld ziet u hoe u de optie *provisionVmAgent* selecteert met een Azure Resource Manager sjabloon:

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

Als u de agents niet hebt geïnstalleerd, kunt u bepaalde Azure-Services, zoals Azure Backup of Azure-beveiliging, niet gebruiken. Deze services vereisen een uitbrei ding die moet worden geïnstalleerd. Als u een virtuele machine zonder Vleugela hebt geïmplementeerd, kunt u later de meest recente versie van de agent installeren.

### <a name="manual-installation"></a>Handmatige installatie
De Windows VM-agent kan hand matig worden geïnstalleerd met een Windows Installer-pakket. Hand matige installatie kan nodig zijn wanneer u een aangepaste VM-installatie kopie maakt die is geïmplementeerd in Azure. Als u de Windows VM-agent hand matig wilt installeren, [downloadt u het installatie programma van de VM-agent](https://go.microsoft.com/fwlink/?LinkID=394789). De VM-agent wordt ondersteund op Windows Server 2008 R2 of hoger.

U kunt de VM-agent installeren door te dubbel klikken op het Windows Installer-bestand. Voer de volgende opdracht uit voor een geautomatiseerde of installatie zonder toezicht van de VM-agent:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>De VM-agent detecteren

### <a name="powershell"></a>PowerShell

De Azure Resource Manager Power shell-module kan worden gebruikt om informatie over virtuele Azure-machines op te halen. Als u informatie wilt weer geven over een virtuele machine, zoals de inrichtings status van de Azure VM-agent, gebruikt u [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

In de volgende gecomprimeerde voorbeeld uitvoer ziet u de eigenschap *ProvisionVMAgent* geneste in *OSProfile*. Deze eigenschap kan worden gebruikt om te bepalen of de VM-agent is geïmplementeerd op de VM:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Het volgende script kan worden gebruikt om een beknopt overzicht van de VM-namen en de status van de VM-agent te retour neren:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Hand matige detectie

Wanneer u bent aangemeld bij een Windows-VM, kan taak beheer worden gebruikt voor het onderzoeken van actieve processen. Als u wilt controleren of de Azure VM-agent is, opent u taak beheer, klikt u op het tabblad *Details* en zoekt u naar een proces naam **WindowsAzureGuestAgent. exe**. De aanwezigheid van dit proces geeft aan dat de VM-agent is geïnstalleerd.


## <a name="upgrade-the-vm-agent"></a>De VM-agent bijwerken
De Azure VM-agent voor Windows wordt automatisch bijgewerkt. Wanneer er nieuwe virtuele machines worden geïmplementeerd in azure, ontvangen ze de nieuwste VM-agent op de VM-inrichtings tijd. Aangepaste VM-installatie kopieën moeten hand matig worden bijgewerkt met de nieuwe VM-agent op het moment waarop de installatie kopie wordt gemaakt.


## <a name="next-steps"></a>Volgende stappen
Zie [overzicht van virtuele machines en functies van Azure](overview.md)voor meer informatie over VM-uitbrei dingen.
