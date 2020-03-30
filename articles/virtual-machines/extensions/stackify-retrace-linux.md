---
title: Stackify Retrace Azure Linux Agent Extension
description: Implementeer de Stackify Retrace Linux-agent op een Linux virtuele machine.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: akjosh
ms.openlocfilehash: 5914947bd994ee405f253e34c3dd919dd6561898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253791"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify Retrace Linux Agent Extensie

## <a name="overview"></a>Overzicht

Stackify biedt producten die details over uw toepassing bijhouden om problemen snel te vinden en op te lossen. Voor ontwikkelaarsteams is Retrace een volledig geïntegreerde, multi-environment, app performance super-power. Het combineert verschillende tools die elk ontwikkelingsteam nodig heeft.

Retrace is de enige tool die alle volgende mogelijkheden biedt in alle omgevingen in één platform.

* Application performance management (APM)
* Toepassing- en serverlogboekregistratie
* Fouttracking en -bewaking
* Server-, toepassings- en aangepaste statistieken

**Over Stackify Linux Agent Extension**

Deze extensie biedt een installatiepad voor de Linux-agent voor Retrace. 

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem 

De Retrace-agent kan worden uitgevoerd tegen deze Linux-distributies

| Distributie | Versie |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 en 17.04 |
| Debian | 7,9+ en 8,2+, 9 |
| Red Hat | 6,7+, 7,1+ |
| CentOS | 6,3+, 7,0+ |

### <a name="internet-connectivity"></a>Internetconnectiviteit

De Stackify Agent-extensie voor Linux vereist dat de beoogde virtuele machine is verbonden met het internet. 

Mogelijk moet u uw netwerkconfiguratie aanpassen om verbindingen https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewallmet Stackify toe te staan, zie . 


## <a name="extension-schema"></a>Extensieschema

---

De volgende JSON toont het schema voor de uitbreiding Stackify Retrace Agent. De uitbreiding `environment` vereist `activationKey`de en .

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Sjabloonimplementatie 

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Het JSON-schema dat in de vorige sectie is beschreven, kan worden gebruikt in een Azure Resource Manager-sjabloon om de Uitbreiding Van Linux Agent van Stackify Retrace uit te voeren tijdens een azure resource manager-sjabloonimplementatie.  

De JSON voor een virtuele machine-extensie kan worden genest in de bron van de virtuele machine of op het hoofd- of hoogste niveau van een JSON-sjabloon voor Resource Manager worden geplaatst. De plaatsing van de JSON is van invloed op de waarde van de resourcenaam en -type. Zie Naam en type instellen voor onderliggende bronnen voor meer informatie.

In het volgende voorbeeld wordt ervan uitgegaan dat de Stackify Retrace Linux-extensie is genest in de bron van de virtuele machine. Bij het nesten van de extensiebron wordt de JSON in het object 'resources': [] object van de virtuele machine geplaatst.

De uitbreiding `environment` vereist `activationKey`de en .

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

Wanneer u de uitbreidingsJSON aan de hoofdmap van de sjabloon plaatst, bevat de bronnaam een verwijzing naar de bovenliggende virtuele machine en het type weerspiegelt de geneste configuratie.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>PowerShell-implementatie

De `Set-AzVMExtension` opdracht kan worden gebruikt om de Stackify Retrace Linux Agent virtuele machine extensie te implementeren naar een bestaande virtuele machine. Voordat u de opdracht uitvoert, moeten de openbare en privéconfiguraties worden opgeslagen in een PowerShell-hashtabel.

De uitbreiding `environment` vereist `activationKey`de en .

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie 

De Azure CLI-tool kan worden gebruikt om de cloudify Retrace Linux Agent-extensie te implementeren in een bestaande virtuele machine.  

De uitbreiding `environment` vereist `activationKey`de en .

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="error-codes"></a>Foutcodes

| Foutcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 10 | Installatiefout | wget is vereist |
| 20 | Installatiefout | python is vereist |
| 30 | Installatiefout | sudo is vereist |
| 40 | Installatiefout | activeringSleutel is vereist |
| 51 | Installatiefout | OS distro niet ondersteund |
| 60 | Installatiefout | omgeving is vereist |
| 70 | Installatiefout | Onbekend |
| 80 | Fout inschakelen | Service-instelling is mislukt |
| 90 | Fout inschakelen | Het opstarten van de service is mislukt |
| 100 | Fout uitschakelen | Servicestop is mislukt |
| 110 | Fout uitschakelen | Serviceverwijdering is mislukt |
| 120 | Fout verwijderen | Servicestop is mislukt |

Als je meer hulp nodig hebt, https://support.stackify.comkun je contact opnemen met Stackify-ondersteuning op .