---
title: Desired state Configuration gebruiken met Virtual Machine Scale Sets | Microsoft Docs
description: Virtual Machine Scale Sets gebruiken met de Azure DSC-uitbrei ding
services: virtual-machine-scale-sets
documentationcenter: ''
author: zjalexander
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
keywords: ''
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: f3da1ed5eabd3a35fe382471314084258b20213b
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166163"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Virtual Machine Scale Sets gebruiken met de Azure DSC-uitbrei ding
[Virtual Machine Scale sets](virtual-machine-scale-sets-overview.md) kan worden gebruikt met de extensie [-handler Azure desired state Configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Virtuele-machine schaal sets bieden een manier om grote aantallen virtuele machines te implementeren en te beheren, en kunnen in-en uitgroeien in antwoord op belasting. DSC wordt gebruikt voor het configureren van de virtuele machines zodra deze online zijn, zodat ze de productie software uitvoeren.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Verschillen tussen de implementatie van Virtual Machines en Virtual Machine Scale Sets
De onderliggende sjabloon structuur voor een schaalset voor virtuele machines wijkt enigszins af van één virtuele machine. In het bijzonder implementeert één VM uitbrei dingen onder het knoop punt ' informatie '. Er is een invoer van het type ' uitbrei dingen ' waar DSC aan de sjabloon wordt toegevoegd

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

Een knoop punt van een virtuele-machine schaalset heeft een sectie ' Eigenschappen ' met het kenmerk ' VirtualMachineProfile ', ' extensionProfile '. DSC wordt toegevoegd onder ' uitbrei dingen '

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

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Gedrag voor een Schaalset voor virtuele machines
Het gedrag voor een schaalset voor virtuele machines is identiek aan het gedrag voor één virtuele machine. Wanneer een nieuwe virtuele machine wordt gemaakt, wordt deze automatisch ingericht met de DSC-extensie. Als er een nieuwere versie van de WMF wordt vereist door de extensie, wordt de VM opnieuw opgestart voordat deze online is. Zodra deze online is, worden de DSC-configuratie. zip gedownload en op de VM ingericht. Meer informatie vindt u in [het overzicht van Azure DSC-extensies](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Volgende stappen
Bekijk de [Azure Resource Manager-sjabloon voor de DSC-extensie](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Meer informatie over hoe de [DSC-extensie veilig referenties afhandelt](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Zie [Introduction to the Azure desired state Configuration extension handler](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Engelstalig) voor meer informatie over de Azure DSC-extensie-handler. 

[Ga naar het Power shell-documentatie centrum](/powershell/scripting/dsc/overview/overview)voor meer informatie over Power shell DSC. 

