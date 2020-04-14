---
title: Toepassingsstatusextensie gebruiken met Azure-schaalsets voor virtuele machines
description: Meer informatie over het gebruik van de extensie Toepassingsstatus om de status van uw toepassingen die zijn geïmplementeerd op virtuele machineschaalsets te controleren.
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: mimckitt
ms.openlocfilehash: cb5f1d48bb1a95db004d9da553e19a35071c73b0
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273729"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>De toepassingsstatusextensie gebruiken met virtuele-machineschaalsets
Het bewaken van de status van uw toepassing is een belangrijk signaal voor het beheren en upgraden van uw implementatie. Azure virtuele machineschaalsets bieden ondersteuning voor [rolling upgrades,](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) waaronder [automatische os-image upgrades,](virtual-machine-scale-sets-automatic-upgrade.md)die afhankelijk zijn van statusbewaking van de afzonderlijke exemplaren om uw implementatie te upgraden.

In dit artikel wordt beschreven hoe u de extensie Toepassingsstatus gebruiken om de status van uw toepassingen die zijn geïmplementeerd op virtuele machineschaalsets te controleren.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u bekend bent met:
-   [Azure-extensies](../virtual-machines/extensions/overview.md) voor virtuele machines
-   [Virtuele](virtual-machine-scale-sets-upgrade-scale-set.md) machineschaalsets wijzigen

## <a name="when-to-use-the-application-health-extension"></a>Wanneer de extensie Toepassingsstatus gebruiken
De extensie Toepassingsstatus wordt geïmplementeerd in een instantie met virtuele machineschaalset en rapporteert over de VM-status vanuit de schaalsetinstantie. U de extensie configureren om te sondeopten op een toepassingseindpunt en de status van de toepassing op die instantie bijwerken. Deze instantiestatus wordt gecontroleerd door Azure om te bepalen of een instantie in aanmerking komt voor upgradebewerkingen.

Aangezien de extensie de status rapporteert vanuit een VM, kan de extensie worden gebruikt in situaties waarin externe sondes zoals Application Health Probes (die gebruikmaken van aangepaste Azure Load [Balancer-sondes)](../load-balancer/load-balancer-custom-probe-overview.md)niet kunnen worden gebruikt.

## <a name="extension-schema"></a>Extensieschema

In de volgende JSON wordt het schema voor de extensie Toepassingsstatus weergegeven. De extensie vereist minimaal een "tcp" of "http" aanvraag met een bijbehorende poort of aanvraag pad respectievelijk.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde / Voorbeeld | Gegevenstype
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| uitgever | `Microsoft.ManagedServices` | tekenreeks |
| type | `ApplicationHealthLinux`(Linux), `ApplicationHealthWindows` (Windows) | tekenreeks |
| typeHandlerVersie | `1.0` | int |

### <a name="settings"></a>Instellingen

| Name | Waarde / Voorbeeld | Gegevenstype
| ---- | ---- | ----
| Protocol | `http` of `tcp` | tekenreeks |
| poort | Optioneel wanneer `http`protocol verplicht is wanneer het protocol`tcp` | int |
| requestPath | Verplicht wanneer `http`protocol is , niet toegestaan wanneer protocol is`tcp` | tekenreeks |

## <a name="deploy-the-application-health-extension"></a>De extensie Toepassingsstatus implementeren
Er zijn meerdere manieren om de extensie Toepassingsstatus in te zetten voor uw schaalsets, zoals beschreven in de onderstaande voorbeelden.

### <a name="rest-api"></a>REST-API

In het volgende voorbeeld wordt de extensie Toepassingsstatus (met naam myHealthExtension) toegevoegd aan de extensieProfiel in het schaalsetmodel van een Schaalset op Basis van Windows.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Hiermee `PATCH` u een reeds geïmplementeerde extensie bewerken.

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) om de extensie Toepassingsstatus toe te voegen aan de modeldefinitie van de schaalset.

In het volgende voorbeeld wordt `extensionProfile` de extensie Toepassingsstatus toegevoegd aan het in-het schaalsetmodel van een Op Windows gebaseerde schaalset. Het voorbeeld maakt gebruik van de nieuwe Az PowerShell-module.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

Gebruik [de az vmss-extensieset](/cli/azure/vmss/extension#az-vmss-extension-set) om de extensie Toepassingsstatus toe te voegen aan de modeldefinitie van de schaalset.

In het volgende voorbeeld wordt de extensie Toepassingsstatus toegevoegd aan het schaalsetmodel van een op Linux gebaseerde schaalset.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
De inhoud van het extension.json-bestand.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Problemen oplossen
De uitvoer van extensieuitvoering wordt vastgelegd in bestanden in de volgende mappen:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

De logboeken leggen ook periodiek de status van de toepassing vast.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele machineschaalsets.
