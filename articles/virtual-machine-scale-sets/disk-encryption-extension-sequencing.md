---
title: Azure Disk Encryption en Azure virtual machine scale stelt extensiesequencing in
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure Disk Encryption for Linux IaaS VM's.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: aa638b86b0788b8c274f9dcb3c04c1fc385b4ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279032"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Azure-schijfversleuteling gebruiken met detectie van extensie voor virtuele machineschaal

Extensies zoals Azure-schijfversleuteling kunnen worden toegevoegd aan een Azure-schaal schaal in een bepaalde volgorde. Gebruik hiervoor [extensiesequencing](virtual-machine-scale-sets-extension-sequencing.md). 

In het algemeen moet versleuteling worden toegepast op een schijf:

- Na extensies of aangepaste scripts die de schijven of volumes voorbereiden.
- Voordat extensies of aangepaste scripts die toegang krijgen tot of verbruiken van de gegevens op de versleutelde schijven of volumes.

In beide gevallen `provisionAfterExtensions` wijst de eigenschap aan welke extensie later in de reeks moet worden toegevoegd.

## <a name="sample-azure-templates"></a>Voorbeeld van Azure-sjablonen

Als u Azure Disk Encryption wilt laten toepassen `provisionAfterExtensions` na een andere extensie, plaatst u de eigenschap in het azurediskencryption-extensieblok. 

Hier volgt een voorbeeld met 'CustomScriptExtension', een Powershell-script dat een Windows-schijf initialiseert en opsmaakt, gevolgd door 'AzureDiskEncryption':

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Als u Azure Disk Encryption wilt laten toepassen `provisionAfterExtensions` vóór een andere extensie, plaatst u de eigenschap in het blok van de extensie die moet worden gevolgd.

Hier volgt een voorbeeld met 'AzureDiskEncryption', gevolgd door 'VMDiagnosticsSettings', een extensie die bewakings- en diagnostische mogelijkheden biedt op een Windows-gebaseerde Azure VM:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Zie voor een meer diepgaande sjabloon:
* De Azure Disk Encryption-extensie toepassen na een aangepast shellscript dat de schijf opsmaakt (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over extensiesequencing: [inprovisioning van reeksextensies in virtuele machineschaalsets](virtual-machine-scale-sets-extension-sequencing.md).
- Meer informatie `provisionAfterExtensions` over de eigenschap: [Microsoft.Compute virtualMachineScaleSets/extensions template reference](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Azure-schijfversleuteling voor virtuele machineschaalsets](disk-encryption-overview.md)
- [Een virtuele machineschaalsets versleutelen met de Azure CLI](disk-encryption-cli.md)
- [Een virtuele machineschaalsets versleutelen met de Azure PowerShell](disk-encryption-powershell.md)
- [Een sleutelkluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)
