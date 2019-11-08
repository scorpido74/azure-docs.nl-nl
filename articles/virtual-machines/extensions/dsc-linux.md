---
title: Azure DSC-extensie voor Linux
description: Installeert OMI-en DSC-pakketten zodat een virtuele Azure Linux-machine kan worden geconfigureerd met behulp van desired state Configuration.
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
ms.openlocfilehash: 1825f9f0f5d525c0129341d800ca5949136ae633
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750071"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC-extensie voor Linux (micro soft. OSTCExtensions. DSCForLinux)

Desired state Configuration (DSC) is een beheer platform waarmee u uw IT-en ontwikkelings infrastructuur met configuratie als code kunt beheren.

> ! Let op de DSC-extensie voor Linux en de [Azure monitor extensie van de virtuele machine voor Linux](/azure/virtual-machines/extensions/oms-linux) dat momenteel een conflict oplevert en niet wordt ondersteund in een side-by-side configuratie.  Dit betekent dat u de twee oplossingen niet samen moet gebruiken op dezelfde VM.

De extensie DSCForLinux wordt gepubliceerd en ondersteund door micro soft. Met de uitbrei ding wordt de OMI-en DSC-agent geïnstalleerd op virtuele machines van Azure. De DSC-extensie kan ook de volgende acties uitvoeren


- Registreer de virtuele Linux-machine naar Azure Automation-account om configuraties van Azure Automation Service te kunnen halen (ExtensionAction registreren)
- MOF-configuraties naar de Linux-VM pushen (push ExtensionAction)
- Meta MOF-configuratie Toep assen op de virtuele Linux-machine voor het configureren van een pull-server voor het ophalen van knooppunt configuratie (pull-ExtensionAction)
- Aangepaste DSC-modules installeren op de virtuele Linux-machine (ExtensionAction installeren)
- Aangepaste DSC-modules verwijderen van de virtuele Linux-machine (ExtensionAction verwijderen)

 

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

De DSC Linux-extensie ondersteunt alle [Linux-distributies die zijn goedgekeurd op Azure](/azure/virtual-machines/linux/endorsed-distros) , met uitzonde ring van:

| Distributie | Versie |
|---|---|
| Debian | alle versies |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>Internetconnectiviteit

De DSCForLinux-extensie vereist dat de virtuele doel machine is verbonden met internet. Voor het registreren van de extensie is bijvoorbeeld connectiviteit met de Automation-Service vereist. Voor andere acties, zoals pull, pull, is connectiviteit met Azure Storage/github vereist. Dit is afhankelijk van de instellingen van de klant.

## <a name="extension-schema"></a>Extensieschema

### <a name="11-public-configuration"></a>1,1 open bare configuratie

Hier vindt u alle ondersteunde para meters voor de open bare configuratie:

* `FileUri`: (optioneel, String) de URI van het MOF-bestand/meta MOF-bestand/aangepaste ZIP-bestand voor de resource.
* `ResourceName`: (optioneel, String) de naam van de aangepaste resource module
* `ExtensionAction`: (optioneel, String) geeft aan wat een extensie doet. geldige waarden: registreren, pushen, pull, installeren, verwijderen. Als u niets opgeeft, wordt dit beschouwd als een push actie.
* `NodeConfigurationName`: (optioneel, String) de naam van een knooppunt configuratie die moet worden toegepast.
* `RefreshFrequencyMins`: (optioneel, int) geeft aan hoe vaak (in minuten) DSC probeert de configuratie van de pull-server op te halen. 
       Als de configuratie op de pull-server verschilt van het huidige op het doel knooppunt, wordt deze gekopieerd naar de in behandeling zijnde Store en toegepast.
* `ConfigurationMode`: (optioneel, String) Hiermee geeft u op hoe DSC de configuratie moet Toep assen. Geldige waarden zijn: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (optioneel, int) geeft aan hoe vaak (in minuten) DSC ervoor zorgt dat de configuratie de gewenste status heeft.

> [!NOTE]
> Als u een versie < 2,3 gebruikt, is de modus parameter hetzelfde als ExtensionAction. De modus lijkt een overbelaste term te zijn. Om Verwar ring te voor komen, wordt ExtensionAction gebruikt vanaf 2,3-versie. Voor achterwaartse compatibiliteit ondersteunt de uitbrei ding zowel de modus als de ExtensionAction. 
>

### <a name="12-protected-configuration"></a>1,2 beveiligde configuratie

Hier vindt u alle ondersteunde beveiligings configuratie parameters:

* `StorageAccountName`: (optioneel, String) de naam van het opslag account dat het bestand bevat
* `StorageAccountKey`: (optioneel, String) de sleutel van het opslag account dat het bestand bevat
* `RegistrationUrl`: (optioneel, String) de URL van het Azure Automation account
* `RegistrationKey`: (optioneel, String) de toegangs sleutel van het Azure Automation-account


## <a name="scenarios"></a>Scenario's

### <a name="register-to-azure-automation-account"></a>Registreren bij Azure Automation-account
beveiligd. json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
openbaar. json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

Power shell-indeling
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Een MOF-configuratie bestand (in Azure Storage account) Toep assen op de VM

beveiligd. json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

openbaar. json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

Power shell-indeling
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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Een MOF-configuratie bestand (in open bare opslag) Toep assen op de VM

openbaar. json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Power shell-indeling
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Een meta MOF-configuratie bestand (in Azure Storage account) Toep assen op de VM

beveiligd. json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

openbaar. json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

Power shell-indeling
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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Een meta MOF-configuratie bestand (in open bare opslag) Toep assen op de VM
openbaar. json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
Power shell-indeling
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Een aangepaste resource module (ZIP-bestand in Azure Storage account) installeren op de VM
beveiligd. json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
openbaar. json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

Power shell-indeling
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Een aangepaste resource module (ZIP-bestand in open bare opslag) installeren op de VM
openbaar. json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
Power shell-indeling
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Een aangepaste resource module verwijderen van de VM
openbaar. json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
Power shell-indeling
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. Sjablonen zijn ideaal bij het implementeren van een of meer virtuele machines waarvoor de implementatie configuratie na een onboarding naar Azure Automation vereist. 

Het voor beeld van een resource manager-sjabloon is [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) en [201-DSC-Linux-Public-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Voor meer informatie over Azure Resource Manager-sjabloon gaat u naar [ontwerp Azure Resource Manager sjablonen](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Implementatie van Azure CLI

### <a name="21-using-azure-cliazure-cli"></a>2,1. [**Azure cli**] [Azure-cli] gebruiken
Voordat u de DSCForLinux-extensie implementeert, moet u uw `public.json` en `protected.json`configureren volgens de verschillende scenario's in sectie 3.

#### <a name="211-classic"></a>2.1.1. Klassiek
De klassieke modus wordt ook Azure Service Management-modus genoemd. U kunt overschakelen naar het bestand door het volgende uit te voeren:
```
$ azure config mode asm
```

U kunt de DSCForLinux-extensie implementeren door het volgende uit te voeren:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Voor meer informatie over de nieuwste beschik bare extensie versie voert u de volgende handelingen uit:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>omschreven. Resource Manager
U kunt overschakelen naar Azure Resource Manager modus door uit te voeren:
```
$ azure config mode arm
```

U kunt de DSCForLinux-extensie implementeren door het volgende uit te voeren:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> In Azure Resource Manager modus is `azure vm extension list` nu niet beschikbaar.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2,2. [**Azure PowerShell**] [Azure-Power Shell] gebruiken

#### <a name="221-classic"></a>2.2.1 (klassiek)

U kunt u aanmelden bij uw Azure-account (Azure Service Management-modus) door het volgende uit te voeren:

```powershell>
Add-AzureAccount
```

En implementeer de DSCForLinux-extensie door de volgende handelingen uit te voeren:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

U moet de inhoud van de $privateConfig en $publicConfig wijzigen volgens de verschillende scenario's in de bovenstaande sectie 
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

#### <a name="222resource-manager"></a>2.2.2. Resource Manager

U kunt u aanmelden bij uw Azure-account (Azure Resource Manager modus) door het volgende uit te voeren:

```powershell>
Login-AzAccount
```

Klik [**hier**](../../azure-resource-manager/manage-resources-powershell.md) voor meer informatie over het gebruik van Azure PowerShell met Azure Resource Manager.

U kunt de DSCForLinux-extensie implementeren door het volgende uit te voeren:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

U moet de inhoud van de $privateConfig en $publicConfig wijzigen volgens de verschillende scenario's in de bovenstaande sectie 
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

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit het Azure Portal en met behulp van de Azure CLI. Als u de implementatie status van uitbrei dingen voor een bepaalde virtuele machine wilt bekijken, voert u de volgende opdracht uit met behulp van de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in het volgende bestand:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Fout code: 51 staat voor een niet-ondersteunde distributie of een niet-ondersteunde extensie actie.
In sommige gevallen kan de DSC Linux-uitbrei ding OMI niet installeren wanneer er al een hogere versie van OMI op de computer aanwezig is. [fout bericht: (000003) downgrade niet toegestaan]



### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/community/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.

## <a name="next-steps"></a>Volgende stappen
Zie [virtuele machines en functies voor Linux](features-linux.md)voor meer informatie over uitbrei dingen.
