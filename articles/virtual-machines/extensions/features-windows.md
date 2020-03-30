---
title: Azure VM-extensies en -functies voor Windows
description: Ontdek welke extensies beschikbaar zijn voor virtuele Azure-machines, gegroepeerd op wat ze bieden of verbeteren.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd6439bf1b1f52b8e63819e8e519fc4971d1bc2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066841"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Virtuele machine-extensies en -functies voor Windows

Extensies van virtuele Azure-machines (VM's) zijn kleine toepassingen die configuratie na de implementatie en automatiseringstaken voor Azure-VM's bieden. Als op een virtuele machine bijvoorbeeld software of antivirusbeveiliging moet worden geïnstalleerd of een script moet worden uitgevoerd, kan hiervoor een VM-extensie worden gebruikt. Azure VM-extensies kunnen worden uitgevoerd met de Azure CLI, PowerShell, Azure Resource Manager-sjablonen en de Azure-portal. Extensies kunnen worden gebundeld met een nieuwe VM-implementatie of worden uitgevoerd op een bestaand systeem.

In dit artikel vindt u een overzicht van VM-extensies, vereisten voor het gebruik van Azure VM-extensies en richtlijnen voor het detecteren, beheren en verwijderen van VM-extensies. Dit artikel biedt algemene informatie omdat er veel VM-extensies beschikbaar zijn, elk met een potentieel unieke configuratie. Uitbreidingsspecifieke details zijn te vinden in elk document dat specifiek is voor de afzonderlijke extensie.

 

## <a name="use-cases-and-samples"></a>Gebruikscases en monsters

Er zijn verschillende Azure VM-extensies beschikbaar, elk met een specifieke use case. Voorbeelden zijn:

- PowerShell-configuraties met de gewenste status toepassen op een vm met de DSC-extensie voor Windows. Zie Azure [Desired State-configuratie-extensie voor](dsc-overview.md)meer informatie.
- Configureer de controle van een VM met de VM-extensie Log Analytics Agent. Zie [Azure VM's verbinden met Azure Monitor-logboeken](../../log-analytics/log-analytics-azure-vm-extension.md)voor meer informatie.
- Configureer een Azure VM met Chef. Zie [Azure VM-implementatie automatiseren met Chef.](../../chef/chef-automation.md)
- Configureer de bewaking van uw Azure-infrastructuur met de Datadog-extensie. Zie voor meer informatie de [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Naast processpecifieke extensies is er een Custom Script-extensie beschikbaar voor zowel virtuele Windows- als Linux-machines. Met de Aangepaste Script-extensie voor Windows kan elk PowerShell-script op een vm worden uitgevoerd. Aangepaste scripts zijn handig voor het ontwerpen van Azure-implementaties die configuratie vereisen die verder gaan dan wat native Azure-tooling kan bieden. Zie [Windows VM Custom Script-extensie](custom-script-windows.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u de extensie op de VM wilt afhandelen, moet de Azure Windows Agent zijn geïnstalleerd. Sommige afzonderlijke extensies hebben vereisten, zoals toegang tot bronnen of afhankelijkheden.

### <a name="azure-vm-agent"></a>Azure VM-agent

De Azure VM-agent beheert interacties tussen een Azure VM en de Azure-fabriccontroller. De VM-agent is verantwoordelijk voor veel functionele aspecten van het implementeren en beheren van Azure VM's, waaronder het uitvoeren van VM-extensies. De Azure VM-agent is vooraf geïnstalleerd op Azure Marketplace-afbeeldingen en kan handmatig worden geïnstalleerd op ondersteunde besturingssystemen. De Azure VM-agent voor Windows staat bekend als de Windows Guest-agent.

Zie [Azure virtual machine agent](agent-windows.md)voor informatie over ondersteunde besturingssystemen en installatie-instructies.

#### <a name="supported-agent-versions"></a>Ondersteunde agentversies

Om de best mogelijke ervaring te bieden, zijn er minimale versies van de agent. Raadpleeg [dit artikel](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) voor meer informatie.

#### <a name="supported-oses"></a>Ondersteunde BE's

De Windows Guest-agent draait op meerdere beste's, maar het extensiekader heeft een limiet voor de besdieextensies. Raadpleeg [dit artikel](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
) voor meer informatie.

Sommige extensies worden niet in alle bestes ondersteund en kunnen *foutcode 51, 'Niet-ondersteund besturingssysteem'* uitzenden. Controleer de afzonderlijke extensiedocumentatie op ondersteuning.

#### <a name="network-access"></a>Netwerktoegang

Extensiepakketten worden gedownload uit de Azure Storage Extension repository en extensiestatus uploads worden geplaatst naar Azure Storage. Als u [de ondersteunde](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) versie van de agents gebruikt, hoeft u geen toegang tot Azure Storage in de VM-regio toe te staan, zoals de agent kan gebruiken om de communicatie om te leiden naar de Azure-fabriccontroller voor agentcommunicatie (HostGAPlugin-functie via het geprivilegieerde kanaal op privé-IP [168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)). Als u een niet-ondersteunde versie van de agent hebt, moet u uitgaande toegang tot Azure-opslag in die regio toestaan vanuit de VM.

> [!IMPORTANT]
> Als u de toegang tot *168.63.129.16* hebt geblokkeerd met behulp van de gastfirewall of met een proxy, mislukken extensies, ongeacht het bovenstaande. Poorten 80, 443 en 32526 zijn vereist.

Agents kunnen alleen worden gebruikt om extensiepakketten en rapportagestatus te downloaden. Als een extensieinstallatie bijvoorbeeld een script moet downloaden van GitHub (Custom Script) of toegang tot Azure Storage (Azure Backup) nodig heeft, moeten extra firewall-/netwerkbeveiligingsgroeppoorten worden geopend. Verschillende extensies hebben verschillende eisen, omdat ze toepassingen in hun eigen recht. Voor extensies waarvoor toegang tot Azure Storage of Azure Active Directory vereist is, u toegang verlenen met [Azure NSG-servicetags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) tot opslag of AzureActiveDirectory.

De Windows Guest Agent heeft geen proxyserverondersteuning voor u om agentverkeersverzoeken door te leiden, wat betekent dat de Windows Guest Agent zal vertrouwen op uw aangepaste proxy (als u er een hebt) om toegang te krijgen tot bronnen op internet of op de Host via IP 168.63.129.16.

## <a name="discover-vm-extensions"></a>VM-extensies ontdekken

Er zijn veel verschillende VM-extensies beschikbaar voor gebruik met Azure-VM's. Als u een volledige lijst wilt bekijken, gebruikt u [Get-AzVMExtensionImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage). In het volgende voorbeeld worden alle beschikbare extensies op de *WestUS-locatie* weergegeven:

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>VM-extensies uitvoeren

Azure VM-extensies worden uitgevoerd op bestaande VM's, wat handig is wanneer u configuratiewijzigingen moet aanbrengen of connectiviteit moet herstellen op een reeds geïmplementeerde VM. VM-extensies kunnen ook worden gebundeld met azure resource manager-sjabloonimplementaties. Door extensies met Resource Manager-sjablonen te gebruiken, kunnen Azure VM's worden geïmplementeerd en geconfigureerd zonder tussenkomst na implementatie.

De volgende methoden kunnen worden gebruikt om een extensie uit te voeren tegen een bestaande vm.

### <a name="powershell"></a>PowerShell

Er bestaan verschillende PowerShell-opdrachten voor het uitvoeren van afzonderlijke extensies. Als u een lijst wilt bekijken, gebruikt u [Get-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) en filter *t.o.a. Extensie:*

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Dit biedt uitvoer vergelijkbaar met de volgende:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

In het volgende voorbeeld wordt de extensie Aangepast script gebruikt om een script van een GitHub-opslagplaats naar de virtuele doelmachine te downloaden en vervolgens het script uit te voeren. Zie Overzicht van aangepaste [scriptextensie](custom-script-windows.md)voor meer informatie over de extensie Aangepast script .

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

In het volgende voorbeeld wordt de VM Access-extensie gebruikt om het beheerderswachtwoord van een Windows-vm opnieuw in te stellen op een tijdelijk wachtwoord. Zie [Extern bureaublad-service opnieuw instellen in een Windows-vm](../windows/reset-rdp.md)voor meer informatie over de VM Access-extensie. Zodra u dit hebt uitgevoerd, moet u het wachtwoord bij de eerste aanmelding opnieuw instellen:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

De `Set-AzVMExtension` opdracht kan worden gebruikt om een VM-extensie te starten. Zie de referentie [set-AzVMExtension voor](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension)meer informatie.


### <a name="azure-portal"></a>Azure Portal

VM-extensies kunnen worden toegepast op een bestaande VM via de Azure-portal. Selecteer de VM in de portal, kies **Extensies**en selecteer **Toevoegen**. Kies de gewenste extensie in de lijst met beschikbare extensies en volg de instructies in de wizard.

In het volgende voorbeeld wordt de installatie van de Microsoft Antimalware-extensie van de Azure-portal weergegeven:

![Antimalware-extensie installeren](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

VM-extensies kunnen worden toegevoegd aan een Azure Resource Manager-sjabloon en worden uitgevoerd met de implementatie van de sjabloon. Wanneer u een extensie met een sjabloon implementeert, u volledig geconfigureerde Azure-implementaties maken. De volgende JSON wordt bijvoorbeeld overgenomen uit een Resource Manager-sjabloon en implementeert een set vm's met belastingsbalans en een Azure SQL-database en installeert vervolgens een .NET Core-toepassing op elke VM. De VM-extensie zorgt voor de installatie van de software.

Zie de volledige [resourcemanagersjabloon](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)voor meer informatie .

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Zie [Azure Resource Manager-sjablonen ontwerpen met Windows VM-extensies](../windows/template-description.md#extensions)voor meer informatie over het maken van Resource Manager-sjablonen.

## <a name="secure-vm-extension-data"></a>Gegevens voor VM-extensiebeveiligen

Wanneer u een VM-extensie uitvoert, kan het nodig zijn om gevoelige informatie op te nemen, zoals referenties, namen van opslagaccounten en toegangssleutels voor opslagaccount. Veel VM-extensies bevatten een beveiligde configuratie die gegevens versleutelt en alleen decodeert in de doel-VM. Elke extensie heeft een specifiek beveiligd configuratieschema en elk is gedetailleerd in extensiespecifieke documentatie.

In het volgende voorbeeld wordt een instantie van de aangepaste script-extensie voor Windows weergegeven. De opdracht die moet worden uitgevoerd, bevat een set referenties. In dit voorbeeld wordt de opdracht die moet worden uitgevoerd niet versleuteld:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
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
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Op een Azure IaaS VM die extensies gebruikt, ziet u in de certificatenconsole mogelijk certificaten met het onderwerp **_Windows Azure CRP Certificate Generator_**. Op een klassieke RDFE-vm hebben deze certificaten de ondernaam **_Windows Azure Service Management for Extensions_**.

Deze certificaten beveiligen de communicatie tussen de VM en de host tijdens de overdracht van beveiligde instellingen (wachtwoord, andere referenties) die door extensies worden gebruikt. De certificaten worden gebouwd door de Azure-fabriccontroller en doorgegeven aan de VM-agent. Als u stopt en de VM elke dag start, kan een nieuw certificaat worden gemaakt door de fabriccontroller. Het certificaat wordt opgeslagen in het persoonlijke certificatenarchief van de computer. Deze certificaten kunnen worden verwijderd. De VM-agent maakt indien nodig opnieuw certificaten.

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

#### <a name="listing-extensions-deployed-to-a-vm"></a>Aanbiedingsextensies geïmplementeerd op een vm

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Agent-updates

De Windows Guest Agent bevat alleen *extensieverwerkingscode*, de *Windows Provisioning-code* is gescheiden. U de Windows Guest Agent verwijderen. U de automatische update van de Window Guest Agent niet uitschakelen.

De *code Extensieafhandeling* is verantwoordelijk voor de communicatie met de Azure-structuur en voor het afhandelen van de VM-extensies, zoals installaties, rapportagestatus, het bijwerken van de afzonderlijke extensies en het verwijderen ervan. Updates bevatten beveiligingsoplossingen, bugfixes en verbeteringen aan de *code voor extensieafhandeling.*

Zie [Geïnstalleerde Windows Guest Agent detecteren](agent-windows.md#detect-the-vm-agent)als u wilt controleren welke versie u uitvoert.

#### <a name="extension-updates"></a>Uitbreidingsupdates

Wanneer een extensie-update beschikbaar is, downloadt en upgradet de Windows Guest Agent de extensie. Automatische uitbreidingsupdates zijn *klein* of *hotfix*. U zich aanmelden voor of afmelden voor *kleine* updates van extensies wanneer u de extensie indient. In het volgende voorbeeld ziet u hoe u secundaire versies automatisch upgraden in een Resource Manager-sjabloon met *autoUpgradeMinorVersion: true':*

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

Om de nieuwste minor release bug fixes te krijgen, is het sterk aanbevolen dat u altijd automatische update in uw extensie implementaties te selecteren. Hotfix-updates met beveiligings- of key bugfixes kunnen niet worden afgemeld.

### <a name="how-to-identify-extension-updates"></a>Extensie-updates identificeren

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificeren of de extensie is ingesteld met autoUpgradeMinorVersion op een vm

U aan het VM-model zien of de extensie is ingericht met 'autoUpgradeMinorVersion'. Als u wilt controleren, gebruikt u [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) en geeft u de resourcegroep en de VM-naam als volgt op:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

In het volgende voorbeeld wordt weergegeven dat *autoUpgradeMinorVersion* is ingesteld op *true:*

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificeren wanneer een autoUpgradeMinorVersion is opgetreden

Als u wilt zien wanneer een update voor de extensie heeft plaatsgevonden, controleert u de agentlogboeken op de VM op *C:\WindowsAzure\Logs\WaAppAgent.log*

In het volgende voorbeeld heeft de VM *Microsoft.Compute.CustomScriptExtension 1.8* geïnstalleerd. Een hotfix was beschikbaar voor versie *1.9:*

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Agentmachtigingen

Om zijn taken uit te voeren, moet de agent worden uitgevoerd als *Lokaal systeem*.

## <a name="troubleshoot-vm-extensions"></a>Vm-extensies oplossen

Elke VM-extensie kan stappen voor het oplossen van problemen hebben die specifiek zijn voor de extensie. Wanneer u bijvoorbeeld de extensie Aangepast script gebruikt, zijn de details van de uitvoering van scripts lokaal te vinden op de VM waar de extensie is uitgevoerd. Alle uitbreidingsspecifieke probleemoplossingsstappen worden beschreven in extensiespecifieke documentatie.

De volgende stappen voor het oplossen van problemen zijn van toepassing op alle VM-extensies.

1. Als u het Windows Guest Agent Log wilt controleren, bekijkt u de activiteit wanneer uw extensie werd ingericht in *C:\WindowsAzure\Logs\WaAppAgent.log*

2. Controleer de werkelijke extensielogboeken voor meer informatie in *C:\WindowsAzure\Logs\Plugins\<extensionName>*

3. Controleer extensie specifieke documentatie probleemoplossing secties voor foutcodes, bekende problemen etc.

4. Kijk naar de systeemlogboeken. Controleer op andere bewerkingen die mogelijk de extensie hebben verstoord, zoals een langdurige installatie van een andere toepassing waarvoor exclusieve toegang tot pakketbeheer vereist is.

### <a name="common-reasons-for-extension-failures"></a>Veelvoorkomende redenen voor uitbreidingsfouten

1. Extensies hebben 20 minuten uit te voeren (uitzonderingen zijn de CustomScript-extensies, Chef- en DSC die 90 minuten hebben). Als uw implementatie deze tijd overschrijdt, wordt deze gemarkeerd als een time-out. De oorzaak hiervan kan te wijten zijn aan low resource VM's, andere VM-configuraties / opstarttaken die hoge hoeveelheden resources verbruiken terwijl de extensie probeert in te richten.

2. Minimumvoorwaarden niet voldaan. Sommige extensies hebben afhankelijkheden van VM SKU's, zoals HPC-afbeeldingen. Voor extensies zijn mogelijk bepaalde vereisten voor netwerktoegang vereist, zoals communicatie met Azure Storage of openbare services. Andere voorbeelden zijn toegang tot pakketrepositories, zonder schijfruimte of beveiligingsbeperkingen.

3. Exclusieve package manager toegang. In sommige gevallen u een langlopende VM-configuratie en extensie-installatie tegenkomen, waarbij ze allebei exclusieve toegang tot de pakketbeheerder nodig hebben.

### <a name="view-extension-status"></a>Status van extensie weergeven

Nadat een VM-extensie is uitgevoerd tegen een VM, gebruikt u [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) om de extensiestatus terug te geven. *Substatussen[0]* tonen aan dat de extensiebepaling is geslaagd, wat betekent dat deze met succes is geïmplementeerd op de VM, maar dat de uitvoering van de extensie binnen de VM is mislukt, *Substatussen[1]*.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

De uitvoer is vergelijkbaar met de volgende voorbeelduitvoer:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

De status van extensie-uitvoering is ook te vinden in de Azure-portal. Als u de status van een extensie wilt weergeven, selecteert u de VM, kiest **u Extensies**en selecteert u vervolgens de gewenste extensie.

### <a name="rerun-vm-extensions"></a>VM-extensies opnieuw uitvoeren

Er kunnen gevallen zijn waarin een VM-extensie opnieuw moet worden uitgevoerd. U een extensie opnieuw uitvoeren door deze te verwijderen en vervolgens de extensie opnieuw uitvoeren met een uitvoeringsmethode naar keuze. Als u een extensie wilt verwijderen, gebruikt u [Remove-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) als volgt:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

U ook als volgt een extensie in de Azure-portal verwijderen:

1. Selecteer een VM.
2. Kies **Extensies**.
3. Selecteer de gewenste extensie.
4. Kies **Verwijderen**.

## <a name="common-vm-extensions-reference"></a>Verwijzing naar algemene VM-extensies
| Naam extensie | Beschrijving | Meer informatie |
| --- | --- | --- |
| Aangepaste scriptextensie voor Windows |Scripts uitvoeren tegen een virtuele Azure-machine |[Aangepaste scriptextensie voor Windows](custom-script-windows.md) |
| DSC-extensie voor Windows |PowerShell DSC -extensie (Gewenste statusconfiguratie) |[DSC-extensie voor Windows](dsc-overview.md) |
| Azure Diagnostics-extensie |Azure Diagnostics beheren |[Azure Diagnostics-extensie](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM Access-extensie |Gebruikers en referenties beheren |[VM Access-extensie voor Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Volgende stappen

Zie overzicht van [Azure-functies voor virtuele machine-extensies en -functies](overview.md)voor meer informatie over VM-extensies.
