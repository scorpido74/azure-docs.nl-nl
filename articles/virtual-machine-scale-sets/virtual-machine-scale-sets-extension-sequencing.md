---
title: Extensiesequencing gebruiken met Azure-setschaalsets voor virtuele machines
description: Meer informatie over het inrichten van extensies bij het implementeren van meerdere extensies op virtuele machineschaalsets.
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: cde3fb8b56d8509a45bde00dde55e3c69d015b8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278056"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Sequentieuitbreiding in virtuele machineschaalsets
Azure virtual machine extensions bieden mogelijkheden zoals configuratie en beheer na implementatie, bewaking, beveiliging en meer. Productie-implementaties gebruiken doorgaans een combinatie van meerdere extensies die zijn geconfigureerd voor de VM-exemplaren om de gewenste resultaten te bereiken.

Wanneer u meerdere extensies op een virtuele machine gebruikt, is het belangrijk om ervoor te zorgen dat extensies waarvoor dezelfde BE-resources nodig zijn, deze resources niet tegelijkertijd proberen te verkrijgen. Sommige extensies zijn ook afhankelijk van andere extensies om vereiste configuraties te bieden, zoals omgevingsinstellingen en geheimen. Zonder de juiste volgorde en sequencing kunnen afhankelijke extensie-implementaties mislukken.

In dit artikel wordt beschreven hoe u extensies instellen die moeten worden geconfigureerd voor de VM-exemplaren in virtuele machineschaalsets.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u bekend bent met:
-   [Azure-extensies](../virtual-machines/extensions/overview.md) voor virtuele machines
-   [Virtuele](virtual-machine-scale-sets-upgrade-scale-set.md) machineschaalsets wijzigen

## <a name="when-to-use-extension-sequencing"></a>Wanneer extensiesequencing gebruiken
Sequencing extensies in niet verplicht voor schaalsets, en tenzij opgegeven, extensies kunnen worden ingericht op een schaal set instantie in elke volgorde.

Als uw schaalsetmodel bijvoorbeeld twee extensies heeft - ExtensionA en ExtensionB - die in het model zijn opgegeven, kan een van de volgende inprovisioningsreeksen optreden:
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

Als uw toepassing vereist dat Extensie A altijd vóór extensie B wordt ingericht, moet u extensiesequencing gebruiken zoals beschreven in dit artikel. Met extensiesequencing treedt nu slechts één reeks op:
-   ExtensionA - > ExtensionB

Extensies die niet in een gedefinieerde inrichtingsreeks zijn gespecificeerd, kunnen op elk moment worden ingericht, ook vóór, na of tijdens een gedefinieerde reeks. Extensiesequencing geeft alleen aan dat een specifieke extensie wordt ingericht na een andere specifieke extensie. Het heeft geen invloed op de inrichting van een andere extensie die in het model is gedefinieerd.

Als uw schaalsetmodel bijvoorbeeld drie extensies heeft – Extensie A, Extensie B en Extensie C – die in het model zijn opgegeven en extensie C is ingesteld om te worden ingericht na extensie A, kan een van de volgende inprovisioningsreeksen optreden:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Als u ervoor moet zorgen dat er geen andere extensie is ingericht terwijl de gedefinieerde extensiereeks wordt uitgevoerd, raden we u aan alle extensies in uw schaalsetmodel te rangschikken. In het bovenstaande voorbeeld kan extensie B worden ingesteld om na extensie C zodanig te worden ingericht dat er slechts één reeks kan optreden:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Extensiesequencing gebruiken
Als u extensiebepaling wilt innemen, moet u de extensiedefinitie in het schaalmodel bijwerken om de eigenschap "provisionAfterExtensions" op te nemen, waarin een reeks extensienamen wordt geaccepteerd. De extensies die in de waarde van de eigenschaparray worden vermeld, moeten volledig worden gedefinieerd in het schaalmodel.

### <a name="template-deployment"></a>Sjabloonimplementatie
In het volgende voorbeeld wordt een sjabloon gedefinieerd waarbij de schaalset drie extensies heeft – ExtensionA, ExtensionB en ExtensionC – zodat extensies in de volgorde zijn ingericht:
-   ExtensionA -> ExtensionB -> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Aangezien de eigenschap "provisionAfterExtensions" een reeks extensienamen accepteert, kan het bovenstaande voorbeeld zodanig worden gewijzigd dat ExtensionC is ingericht na ExtensionA en ExtensionB, maar er is geen bestelling vereist tussen ExtensionA en ExtensionB. De volgende sjabloon kan worden gebruikt om dit scenario te bereiken:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST API
In het volgende voorbeeld wordt een nieuwe extensie met de naam ExtensionC toegevoegd aan een schaalsetmodel. ExtensionC heeft afhankelijkheden van ExtensionA en ExtensionB, die al zijn gedefinieerd in het schaalmodel.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Als ExtensionC eerder in het schaalsetmodel is gedefinieerd en u nu de `PATCH` afhankelijkheden wilt toevoegen, u een uitvoeren om de eigenschappen van de reeds geïmplementeerde extensie te bewerken.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
Wijzigingen in bestaande schaalsetexemplaren worden toegepast bij de volgende [upgrade](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) om de extensie Toepassingsstatus toe te voegen aan de modeldefinitie van de schaalset. Uitbreiding sequencing vereist het gebruik van Az PowerShell 1.2.0 of hoger.

In het volgende voorbeeld wordt `extensionProfile` de [extensie Toepassingsstatus](virtual-machine-scale-sets-health-extension.md) toegevoegd aan het in een schaalsetmodel van een Op Windows gebaseerde schaalset. De extensie Toepassingsstatus wordt ingericht na het inrichten van de [aangepaste scriptextensie](../virtual-machines/extensions/custom-script-windows.md), die al is gedefinieerd in de schaalset.

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
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [de az vmss-extensieset](/cli/azure/vmss/extension#az-vmss-extension-set) om de extensie Toepassingsstatus toe te voegen aan de modeldefinitie van de schaalset. Extensiesequencing vereist het gebruik van Azure CLI 2.0.55 of hoger.

In het volgende voorbeeld wordt de [extensie Toepassingsstatus](virtual-machine-scale-sets-health-extension.md) toegevoegd aan het schaalsetmodel van een op Windows gebaseerde schaalset. De extensie Toepassingsstatus wordt ingericht na het inrichten van de [aangepaste scriptextensie](../virtual-machines/extensions/custom-script-windows.md), die al is gedefinieerd in de schaalset.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Problemen oplossen

### <a name="not-able-to-add-extension-with-dependencies"></a>Niet in staat om extensie toe te voegen met afhankelijkheden?
1. Controleer of de extensies die zijn opgegeven in provisionAfterExtensions worden gedefinieerd in het schaalsetmodel.
2. Zorg ervoor dat er geen cirkelvormige afhankelijkheden worden geïntroduceerd. De volgende reeks is bijvoorbeeld niet toegestaan: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Zorg ervoor dat alle extensies waarop u afhankelijk bent, een eigenschap 'instellingen' hebben onder extensie 'eigenschappen'. Als ExtentionB bijvoorbeeld na ExtensionA moet worden ingericht, moet ExtensionA het veld 'instellingen' hebben onder ExtensionA 'eigenschappen'. U een lege eigenschap 'instellingen' opgeven als de extensie geen vereiste instellingen verplicht stelt.

### <a name="not-able-to-remove-extensions"></a>Niet in staat om extensies te verwijderen?
Zorg ervoor dat de extensies die worden verwijderd, niet worden vermeld onder provisionAfterExtensions voor andere extensies.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele machineschaalsets.
