---
title: Extensie van de virtuele machine Log Analytics voor Windows
description: Implementeer de Log Analytics-agent op virtuele Windows-machines met behulp van een extensie van een virtuele machine.
services: virtual-machines-windows
documentationcenter: ''
author: MicahMcKittrick-MSFT
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
ms.openlocfilehash: 85b97f31e77736603bd0dc7003d4dbfb91a694dc
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470697"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Extensie van de virtuele machine Log Analytics voor Windows

Azure Monitor Logboeken biedt bewakings mogelijkheden voor Cloud-en on-premises assets. De extensie van de virtuele machine van Log Analytics agent voor Windows wordt gepubliceerd en ondersteund door micro soft. De extensie voor de Log Analytics-agent geïnstalleerd op virtuele Azure-machines en virtuele machines voor een bestaande Log Analytics-werkruimte worden ingeschreven. In dit document vindt u informatie over de ondersteunde platforms, configuraties en implementatie opties voor de Log Analytics extensie van de virtuele machine voor Windows.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Raadpleeg het [overzichts artikel log Analytics agent](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) voor meer informatie over de ondersteunde Windows-besturings systemen.

### <a name="agent-and-vm-extension-version"></a>Versie agent en VM-extensie
De volgende tabel bevat een overzicht van de versie van de Windows Log Analytics VM-extensie en Log Analytics agent bundel voor elke release. 

| Log Analytics bundel versie van Windows-agent | Windows VM-extensie versie Log Analytics | Releasedatum | Releaseopmerkingen |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18018 | 1.0.18018 | Oktober 2019 | <ul><li> Kleine oplossingen voor fouten en stabilisatie verbeteringen </li></ul> |
| 10.20.18011 | 1.0.18011 | Juli 2019 | <ul><li> Kleine oplossingen voor fouten en stabilisatie verbeteringen </li><li> Verhoogde MaxExpressionDepth tot 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Juni 2019 | <ul><li> Kleine oplossingen voor fouten en stabilisatie verbeteringen </li><li> De mogelijkheid om standaard referenties uit te scha kelen is toegevoegd bij het maken van een proxy verbinding (ondersteuning voor WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Maart 2019 | <ul><li>Problemen met kleine stabilisatie </li></ul> |
| 10.19.10006 | N.v.t. | Dec 2018 | <ul><li> Problemen met kleine stabilisatie </li></ul> | 
| 8.0.11136 | N.v.t. | Sept 2018 |  <ul><li> Er is ondersteuning toegevoegd voor het detecteren van een wijziging in de resource-ID bij verplaatsen van de VM </li><li> Er is ondersteuning toegevoegd voor de resource-ID voor rapportage bij gebruik van een installatie zonder extensie </li></ul>| 
| 8.0.11103 | N.v.t. |  April 2018 | |
| 8.0.11081 | 1.0.11081 | Nov 2017 | | 
| 8.0.11072 | 1.0.11072 | Sept 2017 | |
| 8.0.11049 | 1.0.11049 | Feb 2017 | |

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center de Log Analytics agent automatisch te voorzien van een verbinding met de standaard Log Analytics-werk ruimte van het Azure-abonnement. Als u van Azure Security Center gebruikmaakt, niet uitgevoerd door de stappen in dit document. Als u dit doet, wordt de geconfigureerde werk ruimte overschreven en wordt de verbinding met Azure Security Center verbroken.

### <a name="internet-connectivity"></a>Internetconnectiviteit
De Log Analytics agent-extensie voor Windows vereist dat de virtuele doel machine is verbonden met internet. 

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de uitbrei ding van de Log Analytics agent. De uitbrei ding vereist de werk ruimte-ID en de werkruimte sleutel van de doel Log Analytics werk ruimte. Deze kunt u vinden in de instellingen voor de werk ruimte in de Azure Portal. Omdat de sleutel van de werkruimte moet worden behandeld als gevoelige gegevens, moet deze worden opgeslagen in de instellingsconfiguratie van een beveiligde. Azure-VM-extensie beveiligde instellingsgegevens versleuteld en alleen op de virtuele doelmachine worden ontsleuteld. Houd er rekening mee dat **workspaceId** en **workspaceKey** hoofdletter gevoelig zijn.

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
### <a name="property-values"></a>Waarden van eigenschappen

| Name | Waarde / voorbeeld |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (bijvoorbeeld)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (bijvoorbeeld) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* de workspaceId wordt consumerId genoemd in de Log Analytics-API.

> [!NOTE]
> Zie Azure [Connect Windows computers to Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)voor extra eigenschappen.

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Het JSON-schema dat in de vorige sectie wordt beschreven, kan worden gebruikt in een Azure Resource Manager sjabloon om de Log Analytics agent-extensie uit te voeren tijdens de implementatie van een Azure Resource Manager-sjabloon. Een voorbeeld sjabloon met de extensie van de Log Analytics agent-VM vindt u in de [Azure Quick Start-galerie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>De sjabloon biedt geen ondersteuning voor het opgeven van meer dan één werk ruimte-ID en werkruimte sleutel wanneer u de agent wilt configureren om te rapporteren aan meerdere werk ruimten. Zie [een werk ruimte toevoegen of verwijderen](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace)als u de agent wilt configureren om te rapporteren aan meerdere werk ruimten.  

De JSON voor een extensie van een virtuele machine kan worden genest in de resource van de virtuele machine of worden geplaatst op het hoofd niveau of op de hoogste niveaus van een JSON-sjabloon van Resource Manager. De plaatsing van de JSON is van invloed op de waarde van de naam en het type van de resource. Zie voor meer informatie [naam en type voor onderliggende resources instellen](../../azure-resource-manager/templates/child-resource-name-type.md). 

In het volgende voor beeld wordt ervan uitgegaan dat de extensie Log Analytics is genest in de resource van de virtuele machine. Bij het nesten van de extensie bron wordt de JSON in het `"resources": []`-object van de virtuele machine geplaatst.


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

Bij het plaatsen van de JSON-extensie in de hoofdmap van de sjabloon, naam van de resource bevat een verwijzing naar de bovenliggende virtuele machine en het type weerspiegelt de geneste configuratie. 

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

## <a name="powershell-deployment"></a>Power shell-implementatie

De opdracht `Set-AzVMExtension` kan worden gebruikt om de extensie van de virtuele machine van Log Analytics agent te implementeren op een bestaande virtuele machine. Voordat u de opdracht uitvoert, moeten de open bare en persoonlijke configuraties worden opgeslagen in een Power shell-Hash-tabel. 

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

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit het Azure Portal en met behulp van de module Azure PowerShell. Als u de implementatie status van extensies voor een bepaalde virtuele machine wilt bekijken, voert u de volgende opdracht uit met behulp van de module Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in bestanden die zijn gevonden in de volgende map:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een Azure-ondersteuning-incident indienen. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
