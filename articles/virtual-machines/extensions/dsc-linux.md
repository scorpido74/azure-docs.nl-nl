---
title: Azure DSC-extensie voor Linux
description: Installeert OMI- en DSC-pakketten zodat een Azure Linux VM kan worden geconfigureerd met de gewenste statusconfiguratie.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 2f04b5ecb2019a77bbb38e97c3869cc0a9447955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250619"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC-extensie voor Linux (Microsoft.OSTCExtensions.DSCForLinux)

Desired State Configuration (DSC) is een beheerplatform dat u gebruiken om uw IT- en ontwikkelingsinfrastructuur te beheren met configuratie als code.

> [!NOTE]
> De DSC-extensie voor Linux en de [Azure Monitor-extensie voor virtuele machines voor Linux](/azure/virtual-machines/extensions/oms-linux) vormen momenteel een conflict en worden niet ondersteund in een side-by-side-configuratie. Gebruik de twee oplossingen niet samen op dezelfde VM.

De DSCForLinux extensie wordt gepubliceerd en ondersteund door Microsoft. De extensie installeert de OMI- en DSC-agent op virtuele Azure-machines. De DSC-extensie kan ook de volgende acties uitvoeren:


- Registreer de Linux VM op een Azure Automation-account om configuraties uit de Azure Automation-service (Register ExtensionAction) te halen.
- Push MOF-configuraties naar de Linux VM (Push ExtensionAction).
- Pas de MOF-configuratie toe op de Linux-VM om een pull-server te configureren om de node-configuratie (Pull ExtensionAction) te trekken.
- Installeer aangepaste DSC-modules op de Linux VM (Install ExtensionAction).
- Verwijder aangepaste DSC-modules uit de Linux VM (Remove ExtensionAction).

 

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

De DSC Linux-extensie ondersteunt alle [Linux-distributies die op Azure zijn goedgekeurd,](/azure/virtual-machines/linux/endorsed-distros) behalve:

| Distributie | Versie |
|---|---|
| Debian | Alle versies |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Internetconnectiviteit

De DSCForLinux extensie vereist dat de beoogde virtuele machine wordt aangesloten op het internet. De registerextensie vereist bijvoorbeeld connectiviteit met de automatiseringsservice. Voor andere acties zoals Pull, Pull, Install vereist connectiviteit met Azure Storage en GitHub. Het hangt af van de instellingen die door de klant.

## <a name="extension-schema"></a>Extensieschema

### <a name="public-configuration"></a>Openbare configuratie

Hier volgen alle ondersteunde openbare configuratieparameters:

* `FileUri`: (optioneel, tekenreeks) De uri van het MOF-bestand, het MOF-bestand of het aangepaste zip-bestand voor resources.
* `ResourceName`: (optioneel, tekenreeks) De naam van de aangepaste resourcemodule.
* `ExtensionAction`: (optioneel, tekenreeks) Geeft aan wat een extensie doet. Geldige waarden zijn Registreren, Pushen, Pullen, Installeren en Verwijderen. Als dit niet is opgegeven, wordt deze standaard beschouwd als een pushactie.
* `NodeConfigurationName`: (optioneel, tekenreeks) De naam van een knooppuntconfiguratie die moet worden toegepast.
* `RefreshFrequencyMins`: (optioneel, int) Geeft aan hoe vaak (in minuten) DSC probeert de configuratie van de pull-server te verkrijgen. 
       Als de configuratie op de pull-server verschilt van de huidige op het doelknooppunt, wordt deze gekopieerd naar het in behandeling zijnde archief en toegepast.
* `ConfigurationMode`: (optioneel, tekenreeks) Geeft aan hoe DSC de configuratie moet toepassen. Geldige waarden zijn ApplyOnly, ApplyAndMonitor en ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (optioneel, int) Hiermee geeft u aan hoe vaak (in minuten) DSC ervoor zorgt dat de configuratie zich in de gewenste staat bevindt.

> [!NOTE]
> Als u een versie eerder dan 2.3 gebruikt, is de parameter modus hetzelfde als ExtensionAction. Mode lijkt een overbelaste term. Om verwarring te voorkomen, wordt ExtensionAction vanaf versie 2.3 verder gebruikt. Voor achterwaartse compatibiliteit ondersteunt de extensie zowel de modus als ExtensionAction. 
>

### <a name="protected-configuration"></a>Beveiligde configuratie

Hier volgen alle ondersteunde beveiligde configuratieparameters:

* `StorageAccountName`: (optioneel, tekenreeks) De naam van het opslagaccount dat het bestand bevat
* `StorageAccountKey`: (optioneel, tekenreeks) De sleutel van het opslagaccount dat het bestand bevat
* `RegistrationUrl`: (optioneel, tekenreeks) De URL van het Azure Automation-account
* `RegistrationKey`: (optioneel, tekenreeks) De toegangssleutel van het Azure Automation-account


## <a name="scenarios"></a>Scenario's

### <a name="register-an-azure-automation-account"></a>Een Azure Automation-account registreren
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell-indeling
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Een MOF-configuratiebestand (in een Azure-opslagaccount) toepassen op de VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell-indeling
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Een MOF-configuratiebestand (in openbare opslag) toepassen op de vm

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell-indeling
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Een meta MOF-configuratiebestand (in een Azure-opslagaccount) toepassen op de VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell-indeling
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Een meta MOF-configuratiebestand (in openbare opslag) toepassen op de VM
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
PowerShell-indeling
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Een aangepaste resourcemodule (een zip-bestand in een Azure-opslagaccount) naar de VM installeren
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell-indeling
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Een aangepaste resourcemodule (een zip-bestand in openbare opslag) naar de VM installeren
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
PowerShell-indeling
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Een aangepaste resourcemodule uit de VM verwijderen
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
PowerShell-indeling
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Sjablonen zijn ideaal wanneer u een of meer virtuele machines implementeert waarvoor een configuratie na de implementatie vereist is, zoals onboarding naar Azure Automation. 

De sample Resource Manager template is [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) en [201-dsc-linux-public-storage-on-ubuntu.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu)

Zie [Azure Resource Manager-sjablonen ontwerpen](../../azure-resource-manager/templates/template-syntax.md)voor meer informatie over de sjabloon Azure Resource Manager.


## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

### <a name="use-azure-cliazure-cli"></a>[Azure CLI][azure-cli] gebruiken
Voordat u de DSCForLinux-extensie `public.json` `protected.json` implementeert, configureert u uw en volgens de verschillende scenario's in sectie 3.

#### <a name="classic"></a>Klassiek

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

De klassieke implementatiemodus wordt ook wel Azure Service Management-modus genoemd. U er naar overschakelen door het uitvoeren van:
```
$ azure config mode asm
```

U de DSCForLinux-extensie implementeren door het uitvoeren van:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Voer voor meer informatie de nieuwste extensieversie uit:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
U overschakelen naar de Azure Resource Manager-modus door het uitvoeren van:
```
$ azure config mode arm
```

U de DSCForLinux-extensie implementeren door het uitvoeren van:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> In de Azure `azure vm extension list` Resource Manager-modus is u voorlopig niet beschikbaar.
>

### <a name="use-azure-powershellazure-powershell"></a>Gebruik [Azure PowerShell][azure-powershell]

#### <a name="classic"></a>Klassiek

U zich aanmelden bij uw Azure-account in de Azure Service Management-modus door het uitvoeren van:

```powershell>
Add-AzureAccount
```

En implementeer de DSCForLinux-extensie door het uitvoeren van:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Wijzig de inhoud van $privateConfig en $publicConfig volgens verschillende scenario's in de vorige sectie.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Resource Manager

U zich aanmelden bij uw Azure-account in de Azure Resource Manager-modus door het uitvoeren van:

```powershell>
Login-AzAccount
```

Zie [Azure Resources beheren met Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md)voor meer informatie over het gebruik van Azure PowerShell met Azure Resource Manager.

U de DSCForLinux-extensie implementeren door het uitvoeren van:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Wijzig de inhoud van $privateConfig en $publicConfig volgens verschillende scenario's in de vorige sectie.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure CLI. Als u de implementatiestatus van extensies voor een bepaalde VM wilt bekijken, voert u de volgende opdracht uit met de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

De uitvoer van extensieuitvoering wordt vastgelegd in het volgende bestand:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Foutcode: 51 staat voor niet-ondersteunde distributie of niet-ondersteunde extensieactie.
In sommige gevallen slaagt De uitbreiding van DSC Linux er niet in omomi te installeren wanneer een hogere versie van OMI reeds in de machine bestaat. [foutreactie: (000003)Downgrade niet toegestaan]



### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, neemt u contact op met de Azure-experts op de [forums MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/community/) U ook een Azure Support-incident indienen. Ga naar de [Azure Support-site](https://azure.microsoft.com/support/options/)en selecteer **Ondersteuning krijgen**. Lees de [veelgestelde vragen](https://azure.microsoft.com/support/faq/)over Microsoft Azure Support voor informatie over het gebruik van Azure Support.

## <a name="next-steps"></a>Volgende stappen
Zie [Virtuele machine-extensies en -functies voor Linux voor](features-linux.md)meer informatie over extensies.
