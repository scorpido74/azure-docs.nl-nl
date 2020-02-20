---
title: Azure Diagnostics-extensie voor Windows
description: Virtuele Windows-machines bewaken met de uitbrei ding Azure Diagnostics
services: virtual-machines-windows
documentationcenter: ''
author: johnkemnetz
manager: ashwink
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: d4ba3e8bbdad72f10c630a056f988ec83e9b1a68
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471615"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Azure Diagnostics-extensie voor Windows-Vm's

## <a name="overview"></a>Overzicht

Met de extensie Azure Diagnostics VM kunt u bewakings gegevens, zoals prestatie meter items en gebeurtenis logboeken, verzamelen van uw Windows-VM. U kunt nauw keurig opgeven welke gegevens u wilt verzamelen en waar u de gegevens wilt gaan, zoals een Azure Storage-account of een Azure Event hub. U kunt deze gegevens ook gebruiken om grafieken te maken in de Azure Portal of metrische waarschuwingen te maken.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

De uitbrei ding Azure Diagnostics kan worden uitgevoerd op Windows 10-client, Windows Server 2008 R2, 2012, 2012 R2 en 2016.

### <a name="internet-connectivity"></a>Internetconnectiviteit

De uitbrei ding Azure Diagnostics vereist dat de virtuele doel machine is verbonden met internet. 

## <a name="extension-schema"></a>Extensieschema

[In dit document worden de Azure Diagnostics extensie schema-en eigenschaps waarden beschreven.](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Het JSON-schema dat in de vorige sectie wordt beschreven, kan worden gebruikt in een Azure Resource Manager sjabloon om de Azure Diagnostics-extensie uit te voeren tijdens de implementatie van een Azure Resource Manager-sjabloon. Zie [controle en diagnose gebruiken met een Windows-VM en Azure Resource Manager sjablonen](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

De Azure CLI kan worden gebruikt om de Azure Diagnostics-extensie te implementeren op een bestaande virtuele machine. Vervang de eigenschappen beveiligde instellingen en instellingen met een geldige JSON van het bovenstaande uitbreidings schema. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Power shell-implementatie

De `Set-AzVMDiagnosticsExtension`-opdracht kan worden gebruikt om de Azure Diagnostics-extensie toe te voegen aan een bestaande virtuele machine. Zie ook [Power shell gebruiken om Azure Diagnostics in te scha kelen op een virtuele machine waarop Windows wordt uitgevoerd](ps-extensions-diagnostics.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure CLI. Als wilt zien de implementatiestatus van extensies voor een bepaalde virtuele machine, voert u de volgende opdracht uit met de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Raadpleeg dit artikel](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) voor een uitgebreidere gids voor probleem oplossing voor de uitbrei ding van de Azure Diagnostics.

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een Azure-ondersteuning-incident indienen. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over de uitbrei ding van de Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Het schema en de versies van de extensie controleren](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)
