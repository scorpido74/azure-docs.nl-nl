---
title: De virtuele-machine-extensie Log Analytics voor Linux
description: Implementeer de Log Analytics-agent op de virtuele linuxmachine met behulp van een virtuele machine-extensie.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: akjosh
ms.openlocfilehash: 9ddac229fc38a91a8b97b24dc2807080b2295758
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250554"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>De virtuele-machine-extensie Log Analytics voor Linux

## <a name="overview"></a>Overzicht

Azure Monitor Logs biedt beheer-, waarschuwings- en waarschuwingsherstelmogelijkheden voor cloud- en on-premises assets. De log analytics virtuele machine extensie voor Linux wordt gepubliceerd en ondersteund door Microsoft. De extensie installeert de Log Analytics-agent op virtuele Azure-machines en schrijft virtuele machines in een bestaande Log Analytics-werkruimte. In dit document worden de ondersteunde platforms, configuraties en implementatieopties voor de extensie voor virtuele machines van Log Analytics voor Linux beschreven.

>[!NOTE]
>Als onderdeel van de voortdurende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor, wordt de OMS-agent voor Windows of Linux de Log Analytics-agent voor Windows en Log Analytics-agent voor Linux genoemd.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Zie het overzichtsartikel van de [Log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) voor meer informatie over de ondersteunde Linux-distributies.

### <a name="agent-and-vm-extension-version"></a>Versie agent- en VM-extensie
In de volgende tabel vindt u een toewijzing van de versie van de VM-extensie Log Analytics en de log-analyse-agentbundel voor elke release. Een link naar de release notes voor de Log Analytics agent bundel versie is inbegrepen. Release notes bevatten details over bug fixes en nieuwe functies beschikbaar voor een bepaalde agent release.  

| Versie van De versie van de systeemsysteem van De Analyse van Linux VM | Log Analytics Agent-bundelversie | 
|--------------------------------|--------------------------|
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center voorziet automatisch in de loganalytics-agent en verbindt deze met een standaard Log Analytics-werkruimte die door ASC is gemaakt in uw Azure-abonnement. Als u Azure Security Center gebruikt, voert u de stappen in dit document niet uit. Hierdoor overschrijft u de geconfigureerde werkruimte en verbreekt de verbinding met Azure Security Center.

### <a name="internet-connectivity"></a>Internetconnectiviteit

De Log Analytics Agent-extensie voor Linux vereist dat de beoogde virtuele machine is verbonden met het internet. 

## <a name="extension-schema"></a>Extensieschema

In de volgende JSON wordt het schema voor de extensie Log Analytics Agent weergegeven. De extensie vereist de werkruimte-id en werkruimtesleutel van de doellogboekanalysewerkruimte. deze waarden zijn [te vinden in uw Log Analytics-werkruimte](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) in de Azure-portal. Omdat de werkruimtesleutel moet worden behandeld als gevoelige gegevens, moet deze worden opgeslagen in een configuratie met beveiligde instellingen. Azure VM-extensiebeveiligde instellingsgegevens worden versleuteld en alleen gedecodeerd op de virtuele doelmachine. WerkruimteId **workspaceId** en **werkruimteSleutel** zijn hoofdlettergevoelig.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>Het bovenstaande schema gaat ervan uit dat het op het wortelniveau van de sjabloon wordt geplaatst. Als u deze in de bron van `type` de `name` virtuele machine in de sjabloon plaatst, moeten de eigenschappen en eigenschappen worden gewijzigd, zoals [verder naar beneden](#template-deployment)wordt beschreven.

### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde / Voorbeeld |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| uitgever | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersie | 1.7 |
| workspaceId (bijv. | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (bijv. | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9XffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Sjablonen zijn ideaal bij het implementeren van een of meer virtuele machines waarvoor een configuratie na implementatie vereist is, zoals onboarding in Azure Monitor-logboeken. Een voorbeeld sjabloon resourcebeheer met de VM-extensie Log Analytics Agent is te vinden in de [Azure Quickstart Gallery](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

De JSON-configuratie voor een virtuele machine-extensie kan worden genest in de bron van de virtuele machine of op het hoofd- of hoogste niveau van een JSON-sjabloon voor Resource Manager worden geplaatst. De plaatsing van de JSON-configuratie is van invloed op de waarde van de resourcenaam en -type. Zie [Naam en type voor onderliggende bronnen instellen voor](../../azure-resource-manager/templates/child-resource-name-type.md)meer informatie . 

In het volgende voorbeeld wordt ervan uitgegaan dat de VM-extensie is genest in de bron van de virtuele machine. Bij het nesten van de extensiebron wordt `"resources": []` de JSON in het object van de virtuele machine geplaatst.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Wanneer u de uitbreidingsJSON aan de hoofdmap van de sjabloon plaatst, bevat de bronnaam een verwijzing naar de bovenliggende virtuele machine en het type weerspiegelt de geneste configuratie.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

De Azure CLI kan worden gebruikt om de VM-extensie Log Analytics Agent te implementeren op een bestaande virtuele machine. Vervang de *myWorkspaceKey-waarde* hieronder door uw werkruimtesleutel en de *myWorkspaceId-waarde* door uw werkruimte-id. Deze waarden zijn te vinden in uw Log Analytics-werkruimte in de Azure-portal onder *Geavanceerde instellingen*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure CLI. Als u de implementatiestatus van extensies voor een bepaalde VM wilt bekijken, voert u de volgende opdracht uit met de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

De uitvoer van extensieuitvoering wordt vastgelegd in het volgende bestand:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Foutcodes en hun betekenissen

| Foutcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 9 | Voortijdig bellen inschakelen | [Werk de Azure Linux Agent](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) bij naar de nieuwste beschikbare versie. |
| 10 | VM is al verbonden met een Log Analytics-werkruimte | Als u de vm wilt verbinden met de werkruimte die is opgegeven in het extensieschema, stelt u stopOnMultipleConnections in op false in openbare instellingen of verwijdert u deze eigenschap. Deze VM wordt één keer gefactureerd voor elke werkruimte waaraan deze is verbonden. |
| 11 | Ongeldige config verstrekt aan de uitbreiding | Volg de voorgaande voorbeelden om alle eigenschapswaarden in te stellen die nodig zijn voor implementatie. |
| 17 | Installatiefout log Analytics-pakket | 
| 19 | OMI-pakketinstallatiestoring | 
| 20 | Installatiefout SCX-pakket |
| 51 | Deze extensie wordt niet ondersteund op het werkingssysteem van de VM | |
| 55 | Kan geen verbinding maken met de Azure Monitor-service of vereiste pakketten ontbreken of dpkg-pakketbeheer is vergrendeld| Controleer of het systeem toegang tot internet heeft of dat er een geldige HTTP-proxy is verstrekt. Controleer bovendien de juistheid van de werkruimte-id en controleer of de voorzieningen voor krul en teer zijn geïnstalleerd. |

Aanvullende informatie over probleemoplossing is te vinden in de [Handleiding voor het oplossen van problemen met logboekanalyse-voor-Linux.](../../azure-monitor/platform/vmext-troubleshoot.md)

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.
