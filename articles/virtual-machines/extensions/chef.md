---
title: Chef-extensie voor Azure VM's
description: Implementeer de Chef-client op een virtuele machine met behulp van de Chef VM Extension.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/21/2018
ms.author: akjosh
ms.openlocfilehash: a21b8f2fea7433e9f65fd790321a28ea47a38c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76544715"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Chef VM-extensie voor Linux en Windows

Chef Software levert een DevOps-automatiseringsplatform voor Linux en Windows. Hiermee kunnen zowel fysieke als virtuele serverconfiguraties worden beheerd. De Chef VM Extension is een uitbreiding die chef-kok op virtuele machines mogelijk maakt.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

De CHEF VM-extensie wordt ondersteund op alle [extensieondersteunde besturingssysteem's](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) in Azure.

### <a name="internet-connectivity"></a>Internetconnectiviteit

De Chef VM Extension vereist dat de beoogde virtuele machine is aangesloten op het internet om de Chef Client payload op te halen uit het content delivery network (CDN).  

## <a name="extension-schema"></a>Extensieschema

In de volgende JSON wordt het schema voor de CHEF VM Extension weergegeven. De extensie vereist minimaal de URL van chef-server, de validatieclientnaam en de validatiesleutel voor de Chef-server; deze waarden zijn te `knife.rb` vinden in het bestand in de starter-kit.zip die wordt gedownload wanneer u [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) of een standalone [Chef Server](https://downloads.chef.io/chef-server)installeert. Omdat de validatiesleutel moet worden behandeld als gevoelige gegevens, moet deze worden geconfigureerd onder het element **protectedSettings,** wat betekent dat deze alleen wordt gedecodeerd op de virtuele doelmachine.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.13",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Waarden voor de eigenschap Core

| Name | Waarde / Voorbeeld | Gegevenstype
| ---- | ---- | ----
| apiVersion | `2017-12-01` | tekenreeks (datum) |
| uitgever | `Chef.Bootstrap.WindowsAzure` | tekenreeks |
| type | `LinuxChefClient`(Linux), `ChefClient` (Windows) | tekenreeks |
| typeHandlerVersie | `1210.13` | tekenreeks (dubbel) |

### <a name="settings"></a>Instellingen

| Name | Waarde / Voorbeeld | Gegevenstype | Vereist?
| ---- | ---- | ---- | ----
| instellingen/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | tekenreeks (url) | J |
| instellingen/bootstrap_options/validation_client_name | `myorg-validator` | tekenreeks | J |
| instellingen/runlist | `recipe[mycookbook::default]` | tekenreeks | J |

### <a name="protected-settings"></a>Beveiligde instellingen

| Name | Voorbeeld | Gegevenstype | Vereist?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | tekenreeks | J |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Sjablonen kunnen worden gebruikt om een of meer virtuele machines te implementeren, de Chef-client te installeren, verbinding te maken met de Chef Server en de eerste configuratie op de server uit te voeren, zoals gedefinieerd in de [run-lijst](https://docs.chef.io/run_lists.html)

Een voorbeeld sjabloon Resource Manager met de VM-extensie Chef-ondersteuning is te vinden in de [Azure quickstart-galerie](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

De JSON-configuratie voor een virtuele machine-extensie kan worden genest in de bron van de virtuele machine of op het hoofd- of hoogste niveau van een JSON-sjabloon voor Resource Manager worden geplaatst. De plaatsing van de JSON-configuratie is van invloed op de waarde van de resourcenaam en -type. Zie [Naam en type voor onderliggende bronnen instellen voor](../../azure-resource-manager/resource-manager-template-child-resource.md)meer informatie .

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

De Azure CLI kan worden gebruikt om de CHEF VM-extensie te implementeren in een bestaande VM. Vervang de **validation_key** door de inhoud van uw `.pem` validatiesleutel (dit bestand als extensie).  Vervang **validation_client_name,** **chef_server_url** en **run_list** `knife.rb` door deze waarden uit het bestand in uw Starter Kit.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.13 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Probleemoplossing en ondersteuning

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure CLI. Als u de implementatiestatus van extensies voor een bepaalde VM wilt bekijken, voert u de volgende opdracht uit met de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

De uitvoer van extensieuitvoering wordt vastgelegd in het volgende bestand:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Foutcodes en hun betekenissen

| Foutcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 51 | Deze extensie wordt niet ondersteund op het besturingssysteem van de VM | |

Aanvullende informatie over probleemoplossing is te vinden in de [chef VM Extension readme.](https://github.com/chef-partners/azure-chef-extension)

> [!NOTE]
> Voor iets anders direct gerelateerd aan chef-kok, contact [op chef-kok Support](https://www.chef.io/support/).

## <a name="next-steps"></a>Volgende stappen

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.
