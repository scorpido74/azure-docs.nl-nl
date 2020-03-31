---
title: VM-extensie van Azure Network Watcher Agent voor Windows installeren
description: Implementeer de Network Watcher Agent op windows virtuele machine met behulp van een virtuele machine extensie.
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 998e160edce25b9d466a1db090abcefeb7870172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073692"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Network Watcher Agent virtual machine extension for Windows

## <a name="overview"></a>Overzicht

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) is een netwerkprestatiebewaking-, diagnostische en analyseservice waarmee Azure-netwerken kunnen worden bewaakt. De uitbreiding van de virtuele machine van Network Watcher Agent is een vereiste voor het vastleggen van netwerkverkeer op aanvraag en andere geavanceerde functionaliteit op virtuele Azure-machines.


In dit document worden de ondersteunde platforms en implementatieopties voor de virtuele machineextensie Network Watcher Agent voor Windows beschreven. De installatie van de agent verstoort of vereist geen herstart van de virtuele machine. U de extensie implementeren in virtuele machines die u implementeert. Als de virtuele machine wordt geïmplementeerd door een Azure-service, controleert u de documentatie voor de service om te bepalen of het installeren van extensies in de virtuele machine al dan niet wordt toegestaan.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

De Network Watcher Agent-extensie voor Windows kan worden uitgevoerd tegen windows server 2008 R2, 2012, 2012 R2 en 2016-releases. Nano Server wordt momenteel niet ondersteund.

### <a name="internet-connectivity"></a>Internetconnectiviteit

Sommige van de Network Watcher Agent-functionaliteit vereist dat de beoogde virtuele machine wordt verbonden met het internet. Zonder de mogelijkheid om uitgaande verbindingen tot stand te brengen, kan de Network Watcher Agent geen pakketopnames uploaden naar uw opslagaccount. Zie voor meer informatie de [documentatie van Network Watcher.](../../network-watcher/network-watcher-monitoring-overview.md)

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de Network Watcher Agent-extensie. De extensie vereist geen door de gebruiker geleverde instellingen en is afhankelijk van de standaardconfiguratie.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde / Voorbeeld |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| uitgever | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersie | 1.4 |


## <a name="template-deployment"></a>Sjabloonimplementatie

U Azure VM-extensies implementeren met Azure Resource Manager-sjablonen. U het JSON-schema dat in de vorige sectie is beschreven gebruiken in een Azure Resource Manager-sjabloon om de uitbreiding Network Watcher Agent uit te voeren tijdens een azure resource manager-sjabloonimplementatie.

## <a name="powershell-deployment"></a>PowerShell-implementatie

Gebruik `Set-AzVMExtension` de opdracht om de virtuele machineextensie Network Watcher Agent te implementeren op een bestaande virtuele machine:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Probleemoplossing en ondersteuning

### <a name="troubleshooting"></a>Problemen oplossen

U gegevens over de status van extensie-implementaties ophalen uit de Azure-portal en PowerShell. Als u de implementatiestatus van extensies voor een bepaalde VM wilt bekijken, voert u de volgende opdracht uit met de Azure PowerShell-module:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

De uitvoer van extensieuitvoering wordt vastgelegd in bestanden in de volgende map:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u verwijzen naar de documentatie van de gebruikershandleiding van Network Watcher of contact opnemen met de Azure-experts op de [MSDN Azure- en Stack Overflow-forums.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.
