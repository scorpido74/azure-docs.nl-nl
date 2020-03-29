---
title: Gewenste statusconfiguratie gebruiken met virtuele machineschaalsets
description: Virtuele machineschaalsets gebruiken met de configuratie-extensie Azure Desired State om virtuele machines te configureren.
author: zjalexander
tags: azure-service-management,azure-resource-manager
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: a93a8a9c27be5a1736a50e6c4c4b830980b7d974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278077"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Virtuele machineschaalsets gebruiken met de Azure DSC-extensie
[Virtuele machineschaalsets](virtual-machine-scale-sets-overview.md) kunnen worden gebruikt met de [Azure Desired State Configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) extensiehandler. Virtuele machineschaalsets bieden een manier om grote aantallen virtuele machines te implementeren en te beheren en kunnen elastisch in- en uitschalen als reactie op belasting. DSC wordt gebruikt om de VM's te configureren zoals ze online komen, zodat ze de productiesoftware draaien.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Verschillen tussen implementatie op virtuele machines en virtuele machineschaalsets
De onderliggende sjabloonstructuur voor een virtuele machineschaalset verschilt iets van één vm. In het bijzonder implementeert een enkele VM extensies onder het knooppunt 'virtualMachines'. Er is een vermelding van het type "extensies" waarbij DSC wordt toegevoegd aan de sjabloon

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Een knooppunt voor een virtuele machineschaalset heeft een sectie 'eigenschappen' met het kenmerk 'VirtualMachineProfile', 'extensionProfile'. DSC wordt toegevoegd onder "extensies"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Gedrag voor een virtuele machineschaalset
The behavior for a virtual machine scale set is identical to the behavior for a single VM. Wanneer een nieuwe virtuele machine wordt gemaakt, wordt deze automatisch ingericht met de DSC-extensie. Als de uitbreiding een nieuwere versie van de WMF vereist heeft, wordt de VM opnieuw opgestart voordat deze online komt. Zodra het online is, downloadt het de DSC-configuratie .zip en indient deze op de VM. Meer details vindt u in [het Azure DSC Extension Overview](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Volgende stappen
Onderzoek de [sjabloon Azure Resource Manager voor de DSC-extensie](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ontdek hoe de [DSC-extensie veilig omgaat met referenties.](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 

Zie Inleiding tot de azure desired [state configuration extension handler voor](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)meer informatie over de Azure DSC-extensie. 

Ga voor meer informatie over PowerShell DSC [naar het PowerShell-documentatiecentrum](/powershell/scripting/dsc/overview/overview). 

