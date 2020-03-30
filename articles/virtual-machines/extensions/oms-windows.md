---
title: De virtuele-machine-extensie Log Analytics voor Windows
description: Implementeer de Log Analytics-agent op de virtuele machine van Windows met behulp van een virtuele machine-extensie.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/30/2020
ms.author: akjosh
ms.openlocfilehash: 85977819d30ddc8745eb9231242eb1990222676c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530985"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>De virtuele-machine-extensie Log Analytics voor Windows

Azure Monitor Logs biedt bewakingsmogelijkheden voor cloud- en on-premises assets. De softwareextensie voor virtuele machines van de Log Analytics-agent voor Windows wordt gepubliceerd en ondersteund door Microsoft. De extensie installeert de Log Analytics-agent op virtuele Azure-machines en schrijft virtuele machines in een bestaande Log Analytics-werkruimte. In dit document worden de ondersteunde platforms, configuraties en implementatieopties voor de extensie voor virtuele machines van Log Analytics voor Windows beschreven.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Raadpleeg het overzichtsartikel van de [agent Log Analytics](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) voor meer informatie over de ondersteunde Windows-besturingssystemen.

### <a name="agent-and-vm-extension-version"></a>Versie agent- en VM-extensie
In de volgende tabel vindt u een toewijzing van de versie van de VM-extensie Windows Log Analytics en de log-analyse-agentbundel voor elke release. 

| Versie van Windows-agentbundel log Analytics | Versie van Windows VM-extensie log Analytics | Releasedatum | Releaseopmerkingen |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18029 | 1.0.18029 | Maart 2020   | <ul><li>Voegt ondersteuning voor SHA-2-codeondertekening toe</li><li>Verbetert de installatie en het beheer van VM-extensies</li><li>Hiermee lost u een bug in Azure Arc voor de integratie van servers op</li><li>Voegt een ingebouwde hulpprogramma voor probleemoplossing toe voor klantenondersteuning</li><li>Voegt ondersteuning toe voor extra Azure Government-regio's</li> |
| 10.20.18018 | 1.0.18018 | Oktober 2019 | <ul><li> Kleine bugfixes en stabilisatieverbeteringen </li></ul> |
| 10.20.18011 | 1.0.18011 | Juli 2019 | <ul><li> Kleine bugfixes en stabilisatieverbeteringen </li><li> Verhoogde MaxExpressionDepth tot 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Juni 2019 | <ul><li> Kleine bugfixes en stabilisatieverbeteringen </li><li> Added ability to disable default credentials when making proxy connection (support for WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Maart 2019 | <ul><li>Kleine stabilisatiefixes </li></ul> |
| 10.19.10006 | N.v.t. | December 2018 | <ul><li> Kleine stabilisatiefixes </li></ul> | 
| 8.0.11136 | N.v.t. | Sept 2018 |  <ul><li> Ondersteuning toegevoegd voor het detecteren van resource-ID-wijziging op VM-verplaatsing </li><li> Added Support for reporting resource ID when using non-extension install Added Support for reporting resource ID when using non-extension install Added Support for reporting resource ID when using non-extension install Added Support </li></ul>| 
| 8.0.11103 | N.v.t. |  April 2018 | |
| 8.0.11081 | 1.0.11081 | November 2017 | | 
| 8.0.11072 | 1.0.11072 | Sept 2017 | |
| 8.0.11049 | 1.0.11049 | Februari 2017 | |


### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center voorziet automatisch in de Log Analytics-agent en verbindt deze met de standaard werkruimte Log Analytics van het Azure-abonnement. Als u Azure Security Center gebruikt, voert u de stappen in dit document niet uit. Hierdoor overschrijft u de geconfigureerde werkruimte en verbreekt u de verbinding met Azure Security Center.

### <a name="internet-connectivity"></a>Internetconnectiviteit
De Log Analytics-agentextensie voor Windows vereist dat de beoogde virtuele machine is verbonden met het internet. 

## <a name="extension-schema"></a>Extensieschema

In de volgende JSON wordt het schema voor de extensie van de Log Analytics-agent weergegeven. De extensie vereist de werkruimte-id en werkruimtesleutel van de doellogboekanalysewerkruimte. Deze zijn te vinden in de instellingen voor de werkruimte in de Azure-portal. Omdat de werkruimtesleutel moet worden behandeld als gevoelige gegevens, moet deze worden opgeslagen in een configuratie met beveiligde instellingen. Azure VM-extensiebeveiligde instellingsgegevens worden versleuteld en alleen gedecodeerd op de virtuele doelmachine. WerkruimteId **workspaceId** en **werkruimteSleutel** zijn hoofdlettergevoelig.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde / Voorbeeld |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| uitgever | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersie | 1.0 |
| workspaceId (bijv.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (bijv. | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9XffbrTahyrwv35W0pOqQAU7uQ== |

\*De workspaceId wordt de consumerId genoemd in de Log Analytics API.

> [!NOTE]
> Zie Azure [Connect Windows Computers voor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)extra eigenschappen met Azure Monitor .

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Het JSON-schema dat in de vorige sectie is beschreven, kan worden gebruikt in een Azure Resource Manager-sjabloon om de extensie van de log-analyseagent uit te voeren tijdens een azure resource manager-sjabloonimplementatie. Een voorbeeldsjabloon met de VM-extensie Log Analytics-agent is te vinden in de [Azure Quickstart Gallery.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm) 

>[!NOTE]
>De sjabloon biedt geen ondersteuning voor het opgeven van meer dan één werkruimte-id en werkruimtesleutel wanneer u de agent wilt configureren om aan meerdere werkruimten te rapporteren. Zie [Een werkruimte toevoegen of verwijderen](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace)als u de agent wilt configureren om te rapporteren aan meerdere werkruimten.  

De JSON voor een virtuele machine-extensie kan worden genest in de bron van de virtuele machine of op het hoofd- of hoogste niveau van een JSON-sjabloon voor Resource Manager worden geplaatst. De plaatsing van de JSON is van invloed op de waarde van de resourcenaam en -type. Zie [Naam en type voor onderliggende bronnen instellen voor](../../azure-resource-manager/templates/child-resource-name-type.md)meer informatie . 

In het volgende voorbeeld wordt ervan uitgegaan dat de extensie Log Analytics is genest in de bron van de virtuele machine. Bij het nesten van de extensiebron wordt `"resources": []` de JSON in het object van de virtuele machine geplaatst.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Wanneer u de uitbreidingsJSON aan de hoofdmap van de sjabloon plaatst, bevat de bronnaam een verwijzing naar de bovenliggende virtuele machine en het type weerspiegelt de geneste configuratie. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell-implementatie

De `Set-AzVMExtension` opdracht kan worden gebruikt om de virtuele machineextensie Log Analytics-agent te implementeren op een bestaande virtuele machine. Voordat u de opdracht uitvoert, moeten de openbare en privéconfiguraties worden opgeslagen in een PowerShell-hashtabel. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure PowerShell-module. Als u de implementatiestatus van extensies voor een bepaalde vm wilt bekijken, voert u de volgende opdracht uit met de Azure PowerShell-module.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

De uitvoer van extensieuitvoering wordt vastgelegd in bestanden in de volgende map:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.
