---
title: Azure Monitor Afhankelijkheid virtuele machine extensie voor Windows
description: Implementeer de Azure Monitor Dependency-agent op de virtuele machine van Windows met behulp van een extensie voor virtuele machines.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 27d43af2d5860d287d8b5914379747ae528db34b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250671"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Azure Monitor Afhankelijkheid virtuele machine extensie voor Windows

De functie Azure Monitor for VMs Map krijgt de gegevens van de Microsoft Dependency-agent. De azure VM Dependency agent virtual machine extension for Windows wordt gepubliceerd en ondersteund door Microsoft. De extensie installeert de afhankelijkheidsagent op virtuele Azure-machines. In dit document worden de ondersteunde platforms, configuraties en implementatieopties voor de azure VM Dependency agent virtual machine extension voor Windows beschreven.

## <a name="operating-system"></a>Besturingssysteem

De Azure VM Dependency agent extension voor Windows kan worden uitgevoerd tegen de ondersteunde besturingssystemen die worden vermeld in de sectie [Ondersteunde besturingssystemen](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) van het azure monitor voor VM's-implementatieartikel.

## <a name="extension-schema"></a>Extensieschema

In de volgende JSON wordt het schema voor de Azure VM Dependency agent extension op een Azure Windows VM weergegeven.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde/voorbeeld |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| uitgever | Microsoft.Azure.Monitoring.DependencyAgent |
| type | AfhankelijkheidagentWindows |
| typeHandlerVersie | 9.5 |

## <a name="template-deployment"></a>Sjabloonimplementatie

U de Azure VM-extensies implementeren met Azure Resource Manager-sjablonen. U het JSON-schema dat in de vorige sectie is beschreven in een Azure Resource Manager-sjabloon gebruiken om de azure VM-afhankelijkheidsagent-extensie uit te voeren tijdens een azure resource manager-sjabloonimplementatie.

De JSON voor een virtuele machine-extensie kan worden genest in de virtuele machinebron. U deze ook op het hoofd- of hoogste niveau van een JSON-sjabloon voor Resource Manager plaatsen. De plaatsing van de JSON is van invloed op de waarde van de resourcenaam en -type. Zie [Naam en type voor onderliggende bronnen instellen voor](../../azure-resource-manager/templates/child-resource-name-type.md)meer informatie .

In het volgende voorbeeld wordt ervan uitgegaan dat de extensie Afhankelijkheidsagent is genest in de bron van de virtuele machine. Wanneer u de extensiebron nestt, wordt `"resources": []` de JSON in het object van de virtuele machine geplaatst.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Wanneer u de uitbreidingsJSON aan de hoofdmap van de sjabloon plaatst, bevat de bronnaam een verwijzing naar de bovenliggende virtuele machine. Het type weerspiegelt de geneste configuratie.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell-implementatie

U `Set-AzVMExtension` de opdracht gebruiken om de extensie van de afhankelijkheidsagent virtuele machine te implementeren in een bestaande virtuele machine. Voordat u de opdracht uitvoert, moeten de openbare en privéconfiguraties worden opgeslagen in een PowerShell-hashtabel.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure PowerShell-module. Als u de implementatiestatus van extensies voor een bepaalde VM wilt bekijken, voert u de volgende opdracht uit met de Azure PowerShell-module:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

De uitvoer van extensieuitvoering wordt vastgelegd in bestanden in de volgende map:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning krijgen**. Lees de veelgestelde vragen over [microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.
