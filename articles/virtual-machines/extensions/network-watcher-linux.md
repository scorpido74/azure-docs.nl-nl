---
title: Azure Network Watcher Agent virtual machine extension for Linux
description: Implementeer de Network Watcher Agent op Linux virtuele machine met behulp van een virtuele machine extensie.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: mimckitt
ms.openlocfilehash: 476241ad3d4077f2da0c513e3c989218701232ba
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255750"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>VM-extensie van Network Watcher Agent voor Linux installeren

## <a name="overview"></a>Overzicht

[Azure Network Watcher](/azure/network-watcher/) is een netwerkprestatiebewaking-, diagnostische en analyseservice waarmee azure-netwerken kunnen worden bewaakt. De VM-extensie (Network Watcher Agent) is een vereiste voor sommige functies van Network Watcher op Azure VM's, zoals het vastleggen van netwerkverkeer op aanvraag en andere geavanceerde functionaliteit.

In dit artikel worden de ondersteunde platforms en implementatieopties voor de NETWORK Watcher Agent VM-extensie voor Linux beschreven. De installatie van de agent verstoort of vereist geen herstart van de VM. U de extensie implementeren in virtuele machines die u implementeert. Als de virtuele machine wordt geïmplementeerd door een Azure-service, controleert u de documentatie voor de service om te bepalen of het installeren van extensies in de virtuele machine al dan niet wordt toegestaan.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

De Network Watcher Agent-extensie kan worden geconfigureerd voor de volgende Linux-distributies:

| Distributie | Versie |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 en 8 |
| Red Hat | 6 en 7 |
| Oracle Linux | 6.8+ en 7 |
| SUSE Linux Enterprise Server | 11 en 12 |
| OpenSUSE Leap | 42,3+ |
| CentOS | 6,5+ en 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Internetconnectiviteit

Voor sommige van de functionaliteit van de Network Watcher Agent is een VM verbonden met internet. Zonder de mogelijkheid om uitgaande verbindingen tot stand te brengen, kunnen sommige functies van de Network Watcher Agent defect raken of niet meer beschikbaar zijn. Zie de documentatie van Network Watcher voor meer informatie over de functionaliteit van Network Watcher waarvoor de agent nodig[is.](/azure/network-watcher/)

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de Network Watcher Agent-extensie. De extensie vereist geen instellingen die door de gebruiker zijn geleverd. De extensie is afhankelijk van de standaardconfiguratie.

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
        "type": "NetworkWatcherAgentLinux",
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
| type | NetworkwatcherAgentLinux |
| typeHandlerVersie | 1.4 |

## <a name="template-deployment"></a>Sjabloonimplementatie

U Azure VM-extensies implementeren met een Azure Resource Manager-sjabloon. Als u de extensie Network Watcher Agent wilt implementeren, gebruikt u het vorige json-schema in uw sjabloon.

## <a name="azure-classic-cli-deployment"></a>Azure-klassieke CLI-implementatie

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

In het volgende voorbeeld wordt de VM-extensie Network Watcher Agent geïmplementeerd in een bestaande VM die is geïmplementeerd via het klassieke implementatiemodel:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

In het volgende voorbeeld wordt de VM-extensie Network Watcher Agent geïmplementeerd in een bestaande VM die is geïmplementeerd via Resource Manager:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Probleemoplossing en ondersteuning

### <a name="troubleshooting"></a>Problemen oplossen

U gegevens over de status van extensie-implementaties ophalen met behulp van de Azure-portal of Azure CLI.

In het volgende voorbeeld wordt de implementatiestatus van de NetworkWatcherAgentLinux-extensie voor een VM weergegeven die is geïmplementeerd via Resourcebeheer, met behulp van de Azure CLI:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u verwijzen naar de [documentatie van Network Watcher](/azure/network-watcher/)of contact opnemen met de Azure-experts op de [MSDN Azure- en Stack Overflow-forums.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning krijgen**. Zie de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.
