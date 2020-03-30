---
title: Azure Monitor Afhankelijkheid virtuele machine extensie voor Linux
description: Implementeer de Azure Monitor Dependency-agent op de virtuele linux-machine met behulp van een virtuele machine-extensie.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 82f9c5a67cb056752cf8310be3b7c9f0bd2501e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254038"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Azure Monitor Afhankelijkheid virtuele machine extensie voor Linux

De functie Azure Monitor for VMs Map krijgt de gegevens van de Microsoft Dependency-agent. De azure VM Dependency agent virtual machine extension for Linux wordt gepubliceerd en ondersteund door Microsoft. De extensie installeert de afhankelijkheidsagent op virtuele Azure-machines. In dit document worden de ondersteunde platforms, configuraties en implementatieopties voor de azure VM Dependency agent virtual machine extension voor Linux beschreven.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

De Azure VM Dependency agent extension voor Linux kan worden uitgevoerd tegen de ondersteunde besturingssystemen die worden vermeld in de sectie [Ondersteunde besturingssystemen](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) van het azure monitor voor VM-implementatieartikel.

## <a name="extension-schema"></a>Extensieschema

In de volgende JSON wordt het schema voor de Azure VM Dependency agent extension op een Azure Linux VM weergegeven. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
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
        "type": "DependencyAgentLinux",
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
| type | AfhankelijkheidAgentLinux |
| typeHandlerVersie | 9.5 |

## <a name="template-deployment"></a>Sjabloonimplementatie

U Azure VM-extensies implementeren met Azure Resource Manager-sjablonen. U het JSON-schema dat in de vorige sectie is beschreven in een Azure Resource Manager-sjabloon gebruiken om de azure VM-afhankelijkheidsagent-extensie uit te voeren tijdens een azure resource manager-sjabloonimplementatie.

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
        "type": "DependencyAgentLinux",
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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

U de Azure CLI gebruiken om de VM-extensie afhankelijkheidsagent te implementeren in een bestaande virtuele machine.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure CLI. Als u de implementatiestatus van extensies voor een bepaalde VM wilt bekijken, voert u de volgende opdracht uit met Azure CLI:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

De uitvoer van extensieuitvoering wordt vastgelegd in het volgende bestand:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, neemt u contact op met de Azure-experts op de [forums MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning krijgen**. Lees de veelgestelde vragen over [microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.
