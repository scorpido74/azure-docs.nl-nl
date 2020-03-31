---
title: Azure VM-extensies en -functies voor Linux
description: Ontdek welke extensies beschikbaar zijn voor virtuele Azure-machines, gegroepeerd op wat ze bieden of verbeteren.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.openlocfilehash: 67df46742be52b03bd91af19654fbfac5df29646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250515"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Virtuele machine-extensies en -functies voor Linux

Extensies van virtuele Azure-machines (VM's) zijn kleine toepassingen die configuratie na de implementatie en automatiseringstaken voor Azure-VM's bieden. Als op een virtuele machine bijvoorbeeld software of antivirusbeveiliging moet worden geïnstalleerd of een script moet worden uitgevoerd, kan hiervoor een VM-extensie worden gebruikt. Azure VM-extensies kunnen worden uitgevoerd met de Azure CLI, PowerShell, Azure Resource Manager-sjablonen en de Azure-portal. Extensies kunnen worden gebundeld met een nieuwe VM-implementatie of worden uitgevoerd op een bestaand systeem.

In dit artikel vindt u een overzicht van VM-extensies, vereisten voor het gebruik van Azure VM-extensies en richtlijnen voor het detecteren, beheren en verwijderen van VM-extensies. Dit artikel biedt algemene informatie omdat er veel VM-extensies beschikbaar zijn, elk met een potentieel unieke configuratie. Uitbreidingsspecifieke details zijn te vinden in elk document dat specifiek is voor de afzonderlijke extensie.

## <a name="use-cases-and-samples"></a>Gebruikscases en monsters

Er zijn verschillende Azure VM-extensies beschikbaar, elk met een specifieke use case. Voorbeelden zijn:

- PowerShell-configuraties met de gewenste status toepassen op een VM met de DSC-extensie voor Linux. Zie Azure [Desired State-configuratie-extensie voor](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)meer informatie.
- Configureer de controle van een VM met de VM-extensie Microsoft Monitoring Agent. Zie [Een Linux VM controleren](../linux/tutorial-monitoring.md)voor meer informatie.
- Configureer de bewaking van uw Azure-infrastructuur met de extensie Chef of Datadog. Zie voor meer informatie de [chef-documenten](https://docs.chef.io/azure_portal.html) of [de Datadog-blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Naast processpecifieke extensies is er een Custom Script-extensie beschikbaar voor zowel virtuele Windows- als Linux-machines. Met de Custom Script-extensie voor Linux kan elk Bash-script op een VM worden uitgevoerd. Aangepaste scripts zijn handig voor het ontwerpen van Azure-implementaties die configuratie vereisen die verder gaan dan wat native Azure-tooling kan bieden. Zie [Linux VM Custom Script-extensie](custom-script-linux.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u de extensie op de VM wilt afhandelen, moet de Azure Linux Agent zijn geïnstalleerd. Sommige afzonderlijke extensies hebben vereisten, zoals toegang tot bronnen of afhankelijkheden.

### <a name="azure-vm-agent"></a>Azure VM-agent

De Azure VM-agent beheert interacties tussen een Azure VM en de Azure-fabriccontroller. De VM-agent is verantwoordelijk voor veel functionele aspecten van het implementeren en beheren van Azure VM's, waaronder het uitvoeren van VM-extensies. De Azure VM-agent is vooraf geïnstalleerd op Azure Marketplace-afbeeldingen en kan handmatig worden geïnstalleerd op ondersteunde besturingssystemen. De Azure VM Agent voor Linux staat bekend als de Linux-agent.

Zie [Azure virtual machine agent](agent-linux.md)voor informatie over ondersteunde besturingssystemen en installatie-instructies.

#### <a name="supported-agent-versions"></a>Ondersteunde agentversies

Om de best mogelijke ervaring te bieden, zijn er minimale versies van de agent. Raadpleeg [dit artikel](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) voor meer informatie.

#### <a name="supported-oses"></a>Ondersteunde BE's

De Linux-agent draait op meerdere beste's, maar de extensies kader heeft een limiet voor de BE's die extensies. Raadpleeg [dit artikel](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
) voor meer informatie.

Sommige extensies worden niet in alle bestes ondersteund en kunnen *foutcode 51, 'Niet-ondersteund besturingssysteem'* uitzenden. Controleer de afzonderlijke extensiedocumentatie op ondersteuning.

#### <a name="network-access"></a>Netwerktoegang

Extensiepakketten worden gedownload uit de Azure Storage Extension repository en extensiestatus uploads worden geplaatst naar Azure Storage. Als u [de ondersteunde](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) versie van de agents gebruikt, hoeft u geen toegang tot Azure Storage in de VM-regio toe te staan, zoals de agent kan gebruiken om de communicatie door te verwijzen naar de Azure-fabriccontroller voor agentcommunicatie. Als u een niet-ondersteunde versie van de agent hebt, moet u uitgaande toegang tot Azure-opslag in die regio toestaan vanuit de VM.

> [!IMPORTANT]
> Als u de toegang tot *168.63.129.16* hebt geblokkeerd met behulp van de gastfirewall, mislukken extensies ongeacht het bovenstaande.

Agents kunnen alleen worden gebruikt om extensiepakketten en rapportagestatus te downloaden. Als een extensieinstallatie bijvoorbeeld een script moet downloaden van GitHub (Custom Script) of toegang tot Azure Storage (Azure Backup) nodig heeft, moeten extra firewall-/netwerkbeveiligingsgroeppoorten worden geopend. Verschillende extensies hebben verschillende eisen, omdat ze toepassingen in hun eigen recht. Voor extensies waarvoor toegang tot Azure Storage vereist is, u toegang toestaan met Azure NSG-servicetags voor [opslag.](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)

Om agentverkeeraanvragen om te leiden, heeft de Linux-agent ondersteuning voor proxyserver. Deze proxyserverondersteuning is echter niet van toepassing op extensies. U moet elke afzonderlijke extensie configureren om met een proxy te werken.

## <a name="discover-vm-extensions"></a>VM-extensies ontdekken

Er zijn veel verschillende VM-extensies beschikbaar voor gebruik met Azure-VM's. Gebruik de lijst met az [vm-extensieom](/cli/azure/vm/extension/image#az-vm-extension-image-list)een volledige lijst te bekijken. In het volgende voorbeeld worden alle beschikbare extensies op de *locatie Westus* weergegeven:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>VM-extensies uitvoeren

Azure VM-extensies worden uitgevoerd op bestaande VM's, wat handig is wanneer u configuratiewijzigingen moet aanbrengen of connectiviteit moet herstellen op een reeds geïmplementeerde VM. VM-extensies kunnen ook worden gebundeld met azure resource manager-sjabloonimplementaties. Door extensies met Resource Manager-sjablonen te gebruiken, kunnen Azure VM's worden geïmplementeerd en geconfigureerd zonder tussenkomst na implementatie.

De volgende methoden kunnen worden gebruikt om een extensie uit te voeren tegen een bestaande vm.

### <a name="azure-cli"></a>Azure-CLI

Azure VM-extensies kunnen worden uitgevoerd tegen een bestaande VM met de opdracht [AZ VM-extensieset.](/cli/azure/vm/extension#az-vm-extension-set) In het volgende voorbeeld wordt de extensie Aangepast script uitgevoerd tegen een VM met de naam *myVM* in een resourcegroep met de naam *myResourceGroup*. Vervang de naam van de voorbeeldbrongroep, de\/vm-naam en het script dat moet worden uitgevoerd (https: /raw.githubusercontent.com/me/project/hello.sh) door uw eigen gegevens. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Wanneer de extensie correct wordt uitgevoerd, is de uitvoer vergelijkbaar met het volgende voorbeeld:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

VM-extensies kunnen worden toegepast op een bestaande VM via de Azure-portal. Selecteer de VM in de portal, kies **Extensies**en selecteer **Toevoegen**. Kies de gewenste extensie in de lijst met beschikbare extensies en volg de instructies in de wizard.

In de volgende afbeelding wordt de installatie van de Linux Custom Script-extensie van de Azure-portal weergegeven:

![Aangepaste scriptextensie installeren](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

VM-extensies kunnen worden toegevoegd aan een Azure Resource Manager-sjabloon en worden uitgevoerd met de implementatie van de sjabloon. Wanneer u een extensie met een sjabloon implementeert, u volledig geconfigureerde Azure-implementaties maken. De volgende JSON wordt bijvoorbeeld overgenomen uit een Resource Manager-sjabloon die een set load-balanced VM's en een Azure SQL-database implementeert en vervolgens een .NET Core-toepassing op elke VM installeert. De VM-extensie zorgt voor de installatie van de software.

Zie de volledige [resourcemanagersjabloon](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)voor meer informatie .

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Zie Sjablonen voor Azure Resource Manager ontwerpen voor meer informatie over het maken van Resource [Manager-sjablonen.](../windows/template-description.md#extensions)

## <a name="secure-vm-extension-data"></a>Gegevens voor VM-extensiebeveiligen

Wanneer u een VM-extensie uitvoert, kan het nodig zijn om gevoelige informatie op te nemen, zoals referenties, namen van opslagaccounten en toegangssleutels voor opslagaccount. Veel VM-extensies bevatten een beveiligde configuratie die gegevens versleutelt en alleen decodeert in de doel-VM. Elke extensie heeft een specifiek beveiligd configuratieschema en elk is gedetailleerd in extensiespecifieke documentatie.

In het volgende voorbeeld ziet u een instantie van de Custom Script-extensie voor Linux. De opdracht die moet worden uitgevoerd, bevat een set referenties. In dit voorbeeld wordt de opdracht die moet worden uitgevoerd niet versleuteld:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Als u de **opdracht verplaatst om** de eigenschap uit te voeren naar de **beveiligde** configuratie, wordt de uitvoeringstekenreeks beveiligd, zoals in het volgende voorbeeld wordt weergegeven:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>Hoe worden agents en extensies bijgewerkt?

De agents en extensies delen hetzelfde updatemechanisme. Voor sommige updates zijn geen extra firewallregels vereist.

Wanneer een update beschikbaar is, wordt deze alleen op de virtuele machine geïnstalleerd wanneer er een wijziging is in extensies en andere wijzigingen in het VM-model, zoals:

- Gegevensschijven
- Extensies
- Opstartdiagnosecontainer
- Gast OS geheimen
- VM-grootte
- Netwerkprofiel

Uitgevers maken updates beschikbaar voor regio's op verschillende tijdstippen, dus het is mogelijk dat u VM's in verschillende regio's op verschillende versies hebben.

#### <a name="agent-updates"></a>Agent-updates

De Linux VM-agent bevat *provisioning agentcode* en *extensieverwerkingscode* in één pakket, die niet kan worden gescheiden. U de *inrichtingsagent* uitschakelen wanneer u azure wilt inrichten met behulp van cloud-init. Zie hiervoor [cloud-init gebruiken.](../linux/using-cloud-init.md)

Ondersteunde versies van de Agents kunnen automatische updates gebruiken. De enige code die kan worden bijgewerkt is de *extensieverwerkingscode,* niet de inrichtingscode. De *Provisioning Agent-code* is run-once-code.

De *code Extensieafhandeling* is verantwoordelijk voor de communicatie met de Azure-structuur en voor het afhandelen van de VM-extensies, zoals installaties, rapportagestatus, het bijwerken van de afzonderlijke extensies en het verwijderen ervan. Updates bevatten beveiligingsoplossingen, bugfixes en verbeteringen aan de *code voor extensieafhandeling.*

Wanneer de agent is geïnstalleerd, wordt een bovenliggende daemon gemaakt. Deze ouder spawnt vervolgens een onderliggend proces dat wordt gebruikt om extensies te verwerken. Als er een update beschikbaar is voor de agent, wordt deze gedownload, stopt de ouder het onderliggende proces, verbetert deze en start deze opnieuw op. Als er een probleem is met de update, rolt het bovenliggende proces terug naar de vorige onderliggende versie.

Het bovenliggende proces kan niet automatisch worden bijgewerkt. De ouder kan alleen worden bijgewerkt door een distro pakket update.

Als u wilt controleren welke `waagent` versie u uitvoert, controleert u de volgende gegevens:

```bash
waagent --version
```

De uitvoer lijkt op die in het volgende voorbeeld:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

In het voorgaande voorbeeld uitvoer, de bovenliggende of 'pakket geïmplementeerde versie' is *WALinuxAgent-2.2.17*

De 'Goal state agent' is de auto update versie.

Het wordt ten zeerste aanbevolen dat u altijd automatische update voor de agent, [AutoUpdate.Enabled=y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Als u dit niet hebt ingeschakeld, moet u de agent handmatig blijven bijwerken en geen bug- en beveiligingsoplossingen krijgen.

#### <a name="extension-updates"></a>Uitbreidingsupdates

Wanneer een extensie-update beschikbaar is, downloadt en upgradet de Linux Agent de extensie. Automatische uitbreidingsupdates zijn *klein* of *hotfix*. U zich aanmelden voor of afmelden voor *kleine* updates van extensies wanneer u de extensie indient. In het volgende voorbeeld ziet u hoe u secundaire versies automatisch upgraden in een Resource Manager-sjabloon met *autoUpgradeMinorVersion: true':*

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

Om de nieuwste minor release bug fixes te krijgen, is het sterk aanbevolen dat u altijd automatische update in uw extensie implementaties te selecteren. Hotfix-updates met beveiligings- of key bugfixes kunnen niet worden afgemeld.

### <a name="how-to-identify-extension-updates"></a>Extensie-updates identificeren

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificeren of de extensie is ingesteld met autoUpgradeMinorVersion op een vm

U aan het VM-model zien of de extensie is ingericht met 'autoUpgradeMinorVersion'. Gebruik de az [vm-show](/cli/azure/vm#az-vm-show) en geef de resourcegroep en de VM-naam als volgt om deze te controleren:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

In het volgende voorbeeld wordt weergegeven dat *autoUpgradeMinorVersion* is ingesteld op *true:*

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificeren wanneer een autoUpgradeMinorVersion is opgetreden

Als u wilt zien wanneer er een update van de extensie heeft plaatsgevonden, controleert u de agentlogboeken op de VM bij */var/log/waagent.log*.

In het onderstaande voorbeeld heeft de VM *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025* geïnstalleerd. Een hotfix was beschikbaar voor *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027:*

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Agentmachtigingen

Om zijn taken uit te voeren, moet de agent als root worden *uitgevoerd.*

## <a name="troubleshoot-vm-extensions"></a>Vm-extensies oplossen

Elke VM-extensie kan stappen voor het oplossen van problemen hebben die specifiek zijn voor de extensie. Wanneer u bijvoorbeeld de extensie Aangepast script gebruikt, zijn de details van de uitvoering van scripts lokaal te vinden op de VM waar de extensie is uitgevoerd. Alle uitbreidingsspecifieke probleemoplossingsstappen worden beschreven in extensiespecifieke documentatie.

De volgende stappen voor het oplossen van problemen zijn van toepassing op alle VM-extensies.

1. Als u de Linux Agent Log wilt controleren, bekijkt u de activiteit wanneer uw extensie werd ingericht in */var/log/waagent.log*

2. Controleer de werkelijke extensielogboeken voor meer details in */var/log/azure/\<extensionName>*

3. Controleer secties voor het oplossen van extensiespecifieke documentatie op foutcodes, bekende problemen enz.

3. Kijk naar de systeemlogboeken. Controleer op andere bewerkingen die mogelijk de extensie hebben verstoord, zoals een langdurige installatie van een andere toepassing waarvoor exclusieve toegang tot pakketbeheer vereist is.

### <a name="common-reasons-for-extension-failures"></a>Veelvoorkomende redenen voor uitbreidingsfouten

1. Extensies hebben 20 minuten uit te voeren (uitzonderingen zijn de CustomScript-extensies, Chef- en DSC die 90 minuten hebben). Als uw implementatie deze tijd overschrijdt, wordt deze gemarkeerd als een time-out. De oorzaak hiervan kan te wijten zijn aan low resource VM's, andere VM-configuraties / opstarttaken die hoge hoeveelheden resources verbruiken terwijl de extensie probeert in te richten.

2. Minimumvoorwaarden niet voldaan. Sommige extensies hebben afhankelijkheden van VM SKU's, zoals HPC-afbeeldingen. Voor extensies zijn mogelijk bepaalde vereisten voor netwerktoegang vereist, zoals communicatie met Azure Storage of openbare services. Andere voorbeelden zijn toegang tot pakketrepositories, zonder schijfruimte of beveiligingsbeperkingen.

3. Exclusieve package manager toegang. In sommige gevallen u een langlopende VM-configuratie en extensie-installatie tegenkomen, waarbij ze allebei exclusieve toegang tot de pakketbeheerder nodig hebben.

### <a name="view-extension-status"></a>Status van extensie weergeven

Nadat een VM-extensie tegen een VM is uitgevoerd, gebruikt u [de AZ VM-weergave](/cli/azure/vm#az-vm-get-instance-view) om de extensiestatus als volgt terug te sturen:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

De uitvoer is vergelijkbaar met de volgende voorbeelduitvoer:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

De status van extensie-uitvoering is ook te vinden in de Azure-portal. Als u de status van een extensie wilt weergeven, selecteert u de VM, kiest **u Extensies**en selecteert u vervolgens de gewenste extensie.

### <a name="rerun-a-vm-extension"></a>Een VM-extensie opnieuw uitvoeren

Er kunnen gevallen zijn waarin een VM-extensie opnieuw moet worden uitgevoerd. U een extensie opnieuw uitvoeren door deze te verwijderen en vervolgens de extensie opnieuw uitvoeren met een uitvoeringsmethode naar keuze. Als u een extensie wilt verwijderen, gebruikt u [de az vm-extensie als](/cli/azure/vm/extension#az-vm-extension-delete) volgt:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

U ook als volgt een extensie in de Azure-portal verwijderen:

1. Selecteer een VM.
2. Kies **Extensies**.
3. Selecteer de gewenste extensie.
4. Kies **Verwijderen**.

## <a name="common-vm-extension-reference"></a>Gemeenschappelijke verwijzing naar vm-extensie

| Naam extensie | Beschrijving | Meer informatie |
| --- | --- | --- |
| Aangepaste Script-extensie voor Linux |Scripts uitvoeren tegen een virtuele Azure-machine |[Aangepaste Script-extensie voor Linux](custom-script-linux.md) |
| VM-extensie voor toegang |Weer toegang krijgen tot een virtuele Azure-machine |[VM-extensie voor toegang](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics-extensie |Azure Diagnostics beheren |[Azure Diagnostics-extensie](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM Access-extensie |Gebruikers en referenties beheren |[VM Access-extensie voor Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Volgende stappen

Zie overzicht van [Azure-functies voor virtuele machine-extensies en -functies](overview.md)voor meer informatie over VM-extensies.
