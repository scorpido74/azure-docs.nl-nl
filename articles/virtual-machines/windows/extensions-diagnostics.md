---
title: Azure Diagnostics Extension voor Windows
description: Azure Windows VM's bewaken met de Azure Diagnostics Extension
author: johnkemnetz
manager: ashwink
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: 1d38a3cac5525de6835bbb0f9873cbd0636d44a9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869680"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Azure Diagnostics Extension for Windows VM's Azure Diagnostics Extension for Windows VM's Azure Diagnostics Extension for Windows VM'

## <a name="overview"></a>Overzicht

Met de VM-extensie Azure Diagnostics u bewakingsgegevens, zoals prestatiemeteritems en gebeurtenislogboeken, verzamelen vanuit uw Windows VM. U gedetailleerd opgeven welke gegevens u wilt verzamelen en waar u de gegevens naartoe wilt gaan, zoals een Azure Storage-account of een Azure Event Hub. U deze gegevens ook gebruiken om grafieken te maken in de Azure-portal of metrische waarschuwingen te maken.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

De Azure Diagnostics Extension kan worden uitgevoerd op Windows 10 Client, Windows Server 2008 R2, 2012, 2012 R2 en 2016.

### <a name="internet-connectivity"></a>Internetconnectiviteit

De Azure Diagnostics Extension vereist dat de beoogde virtuele machine is verbonden met het internet. 

## <a name="extension-schema"></a>Extensieschema

[Het azure diagnostics Extension-schema en de eigenschapswaarden worden in dit document beschreven.](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Het JSON-schema dat in de vorige sectie is beschreven, kan worden gebruikt in een Azure Resource Manager-sjabloon om de Azure Diagnostics-extensie uit te voeren tijdens een azure resource manager-sjabloonimplementatie. Zie [Controle en diagnose gebruiken met een Windows VM- en Azure Resource Manager-sjablonen](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

De Azure CLI kan worden gebruikt om de Azure Diagnostics-extensie te implementeren op een bestaande virtuele machine. Vervang de eigenschappen van beveiligde instellingen en instellingen door geldige JSON uit het bovenstaande uitbreidingsschema. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>PowerShell-implementatie

De `Set-AzVMDiagnosticsExtension` opdracht kan worden gebruikt om de Azure Diagnostics-extensie toe te voegen aan een bestaande virtuele machine. Zie ook [PowerShell gebruiken om Azure Diagnostics in te schakelen in een virtuele machine met Windows](ps-extensions-diagnostics.md).

 


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

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure CLI. Als u de implementatiestatus van extensies voor een bepaalde VM wilt bekijken, voert u de volgende opdracht uit met de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Zie dit artikel](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) voor een uitgebreidere handleiding voor het oplossen van problemen voor de Azure Diagnostics-extensie.

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over de Azure Diagnostics Extension](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Het uitbreidingsschema en -versies bekijken](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)
