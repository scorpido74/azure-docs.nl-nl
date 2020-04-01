---
title: Azure Key Vault VM-extensie voor Windows
description: Implementeer een agent die automatische vernieuwing van Key Vault-geheimen uitvoert op virtuele machines met behulp van een extensie voor virtuele machines.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 8e014e7a1c564377582e4503218c4129619daa91
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410738"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Key Vault-extensie voor virtuele machines voor Windows

De Key Vault VM-extensie biedt automatische vernieuwing van certificaten die zijn opgeslagen in een Azure-sleutelkluis. In het bijzonder controleert de extensie een lijst met waargenomen certificaten die zijn opgeslagen in belangrijke kluizen en, bij het detecteren van een wijziging, de bijbehorende certificaten ophaalt en installeert. In dit document worden de ondersteunde platforms, configuraties en implementatieopties voor de Key Vault VM-extensie voor Windows beschreven. 

### <a name="operating-system"></a>Besturingssysteem

De Key Vault VM-extensie ondersteunt onderstaande versies van Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

### <a name="supported-certificate-content-types"></a>Ondersteunde certificaatinhoudstypen

- PKCS #12
- Pem

## <a name="extension-schema"></a>Extensieschema

In de volgende JSON wordt het schema voor de VM-extensie Key Vault weergegeven. De extensie vereist geen beveiligde instellingen - alle instellingen worden beschouwd als openbare informatie. De extensie vereist een lijst met bewaakte certificaten, pollingfrequentie en het doelcertificaatarchief. Met name:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

> [!NOTE]
> Uw waargenomen certificaten-URL's `https://myVaultName.vault.azure.net/secrets/myCertName`moeten van het formulier zijn.
> 
> Dit komt `/secrets` omdat het pad het volledige certificaat retourneert, inclusief de privésleutel, terwijl het `/certificates` pad dat niet doet. Meer informatie over certificaten vindt u hier: [Key Vault-certificaten](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)

### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde / Voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| uitgever | Microsoft.Azure.KeyVault | tekenreeks |
| type | KeyVaultForWindows | tekenreeks |
| typeHandlerVersie | 1.0 | int |
| pollingIntervalins | 3600 | tekenreeks |
| certificateStoreName | MY | tekenreeks |
| linkOnRenewal | false | booleaans |
| certificateStoreLocatie  | LokaalMachine | tekenreeks |
| vereistInitialSync | waar | booleaans |
| waargenomenCertificaten  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | tekenreeksarray


## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Sjablonen zijn ideaal bij het implementeren van een of meer virtuele machines waarvoor certificaten moeten worden vernieuwd na implementatie. De extensie kan worden geïmplementeerd in afzonderlijke VM's of virtuele machineschaalsets. Het schema en de configuratie zijn gemeenschappelijk voor beide sjabloontypen. 

De JSON-configuratie voor een extensie van een virtuele machine moet worden `"resources": []` genest in het fragment van de virtuele `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` machinebron van de sjabloon, met name object voor de sjabloon voor virtuele machines en in het geval van een virtuele machineschaal die onder object is ingesteld.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Azure PowerShell-implementatie

De Azure PowerShell kan worden gebruikt om de Key Vault VM-extensie te implementeren in een bestaande virtuele machine of virtuele machineschaalset. 

* Ga als lid van het nieuwe bedrijf over op een virtuele machine:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Ga als lid van het nieuwe gebruik van de extensie op een virtuele machineschaalset:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

De Azure CLI kan worden gebruikt om de Key Vault VM-extensie te implementeren in een bestaande virtuele machine of virtuele machineschaalset. 
 
* Ga als lid van het nieuwe bedrijf over op een virtuele machine:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Ga als lid van het nieuwe gebruik van de extensie op een virtuele machineschaalset:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Houd rekening met de volgende beperkingen/vereisten:
- Key Vault-beperkingen:
  - Het moet bestaan op het moment van de implementatie 
  - Key Vault Access Policy is ingesteld voor VM/VMSS Identity met MSI


## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure PowerShell. Als u de implementatiestatus van extensies voor een bepaalde vm wilt bekijken, voert u de volgende opdracht uit met de Azure PowerShell.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

De uitvoer van extensieuitvoering wordt vastgelegd in het volgende bestand:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.
