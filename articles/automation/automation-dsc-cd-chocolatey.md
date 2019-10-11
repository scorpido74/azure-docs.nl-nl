---
title: Doorlopende implementatie van configuratie met Azure Automation status met Choco lade
description: DevOps doorlopende implementatie met behulp van Azure Automation State Configuration, DSC en chocolade Package Manager.  Voor beeld met volledige JSON Resource Manager-sjabloon en Power shell-bron.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f4512b79873d7f770b32a452a02c53bc5575bdac
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243603"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Voor beeld van gebruik: doorlopende implementatie naar Virtual Machines met behulp van de configuratie van de automatiserings status en chocolade

In een DevOps-wereld zijn er veel hulpprogram ma's om te helpen bij verschillende punten in de pipeline voor continue integratie. Azure Automation status configuratie is een nieuwe aanvulling op de opties die DevOps teams kunnen gebruiken. In dit artikel wordt gedemonstreerd hoe u doorlopende implementatie (CD) instelt voor een Windows-computer. U kunt de techniek eenvoudig uitbreiden met zo veel Windows-computers als nodig zijn in de rol (bijvoorbeeld een website), en van daaruit naar extra rollen.

![Continue implementatie voor IaaS-Vm's](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Op een hoog niveau

Er is hier een beetje die u hier kunt doen, maar gelukkig kan worden onderverdeeld in twee hoofd processen:

- Code schrijven en testen, vervolgens installatie pakketten maken en publiceren voor primaire en secundaire versies van het systeem.
- Vm's maken en beheren waarmee de code in de pakketten wordt geïnstalleerd en uitgevoerd.  

Zodra beide kern processen zijn geïmplementeerd, is het een korte stap om het pakket dat op een bepaalde VM wordt uitgevoerd, automatisch bij te werken wanneer er nieuwe versies worden gemaakt en geïmplementeerd.

## <a name="component-overview"></a>Overzicht van onderdelen

Pakket beheerders zoals [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) zijn duidelijk bekend in de Linux-wereld, maar niet zo veel in de Windows-wereld.
Het [is een goed idee](https://chocolatey.org/) om de [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) van Scott Hanselman in het onderwerp een fantastische inleiding te hebben. In een kort gezegd kunt u met behulp van de opdracht regel pakketten van een centrale opslag plaats van pakketten installeren in een Windows-systeem. U kunt uw eigen opslag plaats maken en beheren, en Choco lade kan pakketten installeren vanaf elk gewenst aantal opslag plaatsen die u opgeeft.

Desired state Configuration (DSC) ([overzicht](/powershell/scripting/dsc/overview/overview)) is een Power shell-hulp programma waarmee u de gewenste configuratie voor een machine kunt declareren. U kunt bijvoorbeeld zeggen, "Ik wil dat er in Choco lade geïnstalleerd, ik wil IIS installeren, ik wil dat de poort 80 wordt geopend, ik wil dat versie 1.0.0 van mijn website is geïnstalleerd." De DSC Local Configuration Manager (LCM) implementeert die configuratie. Een DSC-pull-server bevat een opslag plaats met configuraties voor uw machines. De LCM wordt op elke computer periodiek gecontroleerd om te zien of de configuratie overeenkomt met de opgeslagen configuratie. Het rapport kan de status rapporteren of proberen de machine weer in te zetten met de opgeslagen configuratie. U kunt de opgeslagen configuratie op de pull-server bewerken om ervoor te zorgen dat een machine of een set van machines wordt uitgelijnd met de gewijzigde configuratie.

Azure Automation is een beheerde service in Microsoft Azure waarmee u verschillende taken kunt automatiseren met runbooks, knoop punten, referenties, resources en assets, zoals planningen en globale variabelen.
Met de configuratie van de Azure Automation-status kunt u deze automatiserings functionaliteit uitbreiden met Power shell DSC-hulpprogram ma's. Hier volgt een geweldig [overzicht](automation-dsc-overview.md).

Een DSC-resource is een module code met specifieke mogelijkheden, zoals het beheren van netwerken, Active Directory of SQL Server. De chocolade DSC-resource weet hoe u toegang kunt krijgen tot een NuGet-server (onder andere), pakketten downloaden, pakketten installeert, enzovoort. Er zijn veel andere DSC-resources in de [PowerShell Gallery](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).
Deze modules worden geïnstalleerd in de pull-server van de configuratie van de Azure Automation status (door u) zodat deze door uw configuraties kunnen worden gebruikt.

Resource Manager-sjablonen bieden een declaratieve manier voor het genereren van uw infra structuur, zoals netwerken, subnetten, netwerk beveiliging en route ring, load balancers, Nic's, Vm's, enzovoort. Hier volgt een [artikel](../azure-resource-manager/resource-manager-deployment-model.md) dat het Resource Manager-implementatie model (declaratief) vergelijkt met het Azure Service Management (ASM of Classic)-implementatie model (verplicht) en worden de belangrijkste resource providers, compute, opslag en het netwerk besproken.

Een belang rijke functie van een resource manager-sjabloon is de mogelijkheid om een VM-extensie te installeren in de virtuele machine wanneer deze is ingericht. Een VM-extensie heeft specifieke mogelijkheden, zoals het uitvoeren van een aangepast script, het installeren van antivirus software of het uitvoeren van een DSC-configuratie script. Er zijn veel andere typen VM-extensies.

## <a name="quick-trip-around-the-diagram"></a>Snelle reis rond het diagram

Vanaf de bovenkant schrijft u uw code, bouwt en test en maakt u vervolgens een installatie pakket.
Choco lade kan verschillende typen installatie pakketten verwerken, zoals MSI, MSU, ZIP. En u beschikt over de volledige kracht van Power shell om de daad werkelijke installatie uit te voeren als Chocolateys systeem eigen mogelijkheden niet echt zijn. Plaats het pakket op een plek die bereikbaar is, een package-opslag plaats. In dit voor beeld wordt een open bare map in een Azure Blob-opslag account gebruikt, maar deze kan zich overal bevinden. Chocolade werkt standaard met NuGet-servers en enkele andere voor het beheer van de meta gegevens van pakketten. In [dit artikel](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) worden de opties beschreven. In dit voor beeld van gebruik wordt NuGet gebruikt. Een Nuspec is meta gegevens over uw pakketten. De Nuspec zijn ' gecompileerd ' in NuPkg en opgeslagen in een NuGet-server. Wanneer uw configuratie een pakket op naam aanvraagt en naar een NuGet-server verwijst, wordt met de chocolade DSC-resource (nu op de VM) het pakket gepakt en voor u geïnstalleerd. U kunt ook een specifieke versie van een pakket aanvragen.

In de linkerbenedenhoek van de afbeelding bevindt zich een Azure Resource Manager sjabloon. In dit voor beeld registreert de VM-extensie de VM met de Azure Automation status configuratie pull-server (dat wil zeggen, een pull-server) als een knoop punt. De configuratie wordt opgeslagen op de pull-server.
Eigenlijk wordt deze twee keer opgeslagen: eenmaal als tekst zonder opmaak en eenmaal gecompileerd als een MOF-bestand (voor degenen die over dergelijke dingen weten.) In de portal is MOF een ' knooppunt configuratie ' (in plaats van simpelweg ' configuratie '). Het is het artefact dat is gekoppeld aan een knoop punt zodat het knoop punt de configuratie kent. In de onderstaande details ziet u hoe u de knooppunt configuratie aan het knoop punt toewijst.

U gaat er zeker van zijn dat u de bit bovenaan of het meren deel van het al hebt. Het maken van de nuspec, het compileren en opslaan van het bestand op een NuGet-server is een kleine ding. En u bent al bezig met het beheren van virtuele machines. Als u de volgende stap voor continue implementatie wilt uitvoeren, moet u de pull-server (eenmaal) instellen, uw knoop punten registreren (eenmaal) en de configuratie daar maken en opslaan (in eerste instantie). Als pakketten worden geüpgraded en geïmplementeerd naar de opslag plaats, vernieuwt u de configuratie en de knooppunt configuratie op de pull-server (Herhaal deze indien nodig).

Als u niet met een resource manager-sjabloon begint, is dat ook OK. Er zijn Power shell-cmdlets die zijn ontworpen om u te helpen bij het registreren van uw virtuele machines met de pull-server en alle andere. Zie dit artikel: [onboarding machines voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md)voor meer informatie.

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Stap 1: de pull-server en het Automation-account instellen

Op een geverifieerde (@no__t 0) Power shell-opdracht regel: (kan enkele minuten duren terwijl de pull-server is ingesteld)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

U kunt uw Automation-account in een van de volgende regio's plaatsen (ook wel-locatie): VS-Oost 2, Zuid-Centraal VS, US Gov-Virginia, Europa-west, Zuidoost-Azië, Japan-Oost, Centraal-India en Australië-zuidoost, Canada-centraal Europa-noord.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Stap 2: aanpassingen van de VM-extensie aan de Resource Manager-sjabloon

Details voor de registratie van de virtuele machine (met behulp van de Power shell DSC-extensie) die is opgenomen in deze [Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)start-sjabloon.
Met deze stap wordt de nieuwe virtuele machine geregistreerd bij de pull-server in de lijst met status configuratie knooppunten. In deze registratie wordt de knooppunt configuratie opgegeven die op het knoop punt moet worden toegepast. Deze knooppunt configuratie hoeft nog niet te bestaan op de pull-server. het is dus niet in stap 4 waar dit voor de eerste keer gebeurt. Maar u moet in stap 2 de naam van het knoop punt en de naam van de configuratie hebben bepaald. In dit voor beeld is het knoop punt ' isvbox ' en de configuratie is ' ISVBoxConfig '. De naam van de knooppunt configuratie (die moet worden opgegeven in DeploymentTemplate. json) is dus ' ISVBoxConfig. isvbox '.

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Stap 3: de vereiste DSC-resources toevoegen aan de pull-server

De PowerShell Gallery is instrumenteel om DSC-resources in uw Azure Automation-account te installeren.
Ga naar de gewenste resource en klik op de knop implementeren in Azure Automation.

![PowerShell Gallery-voor beeld](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Met een andere techniek die onlangs aan Azure Portal is toegevoegd, kunt u nieuwe modules ophalen of bestaande modules bijwerken. Klik op de resource van het Automation-account, de tegel assets en tenslotte op de tegel modules. Met het pictogram Browse Gallery kunt u de lijst met modules in de galerie bekijken, inzoomen op Details en uiteindelijk importeren in uw Automation-account. Dit is een uitstekende manier om uw modules van tijd tot tijd up-to-date te houden. En met de import functie wordt gecontroleerd op afhankelijkheden met andere modules om te controleren of er niets is gesynchroniseerd.

Of de hand matige aanpak. De mapstructuur van een Power shell-integratie module voor een Windows-computer is iets anders dan de mapstructuur die wordt verwacht door de Azure Automation.
Dit vereist een beetje aanpassen van uw onderdeel. Maar het is niet moeilijk en wordt slechts één keer per resource uitgevoerd (tenzij u het in de toekomst wilt bijwerken). Zie dit artikel: [integratie modules ontwerpen voor Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/) voor meer informatie over het ontwerpen van Power shell-integratie modules

- Installeer de module die u nodig hebt op uw werk station als volgt:
  - [Windows Management Framework (V5](https://aka.ms/wmf5latest) ) installeren (niet vereist voor Windows 10)
  - `Install-Module –Name MODULE-NAME` <: haalt de module op uit de PowerShell Gallery
- De map module kopiëren van `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` naar een tijdelijke map
- Voor beelden en documentatie uit de hoofdmap verwijderen
- Post de hoofdmap, waarbij het ZIP-bestand precies hetzelfde is als de map 
- Plaats het ZIP-bestand in een bereik bare HTTP-locatie, zoals Blob Storage in een Azure Storage-account.
- Deze Power shell uitvoeren:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

Het opgenomen voor beeld voert de volgende stappen uit voor cChoco en xNetworking. Zie de [opmerkingen](#notes) voor speciale verwerking voor cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Stap 4: de knooppunt configuratie toevoegen aan de pull-server

Er is niets speciaal voor de eerste keer dat u uw configuratie in de pull-server importeert en compileert. Alle volgende import/compilaties van dezelfde configuratie zien er precies hetzelfde uit. Telkens wanneer u het pakket bijwerkt en het moet worden gepusht naar productie, voert u deze stap uit nadat u hebt gecontroleerd of het configuratie bestand juist is, met inbegrip van de nieuwe versie van uw pakket. Hier ziet u het configuratie bestand en Power shell:

ISVBoxConfig. ps1:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

New-ConfigurationScript. ps1:

```powershell
Import-AzureRmAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Deze stappen resulteren in een nieuwe knooppunt configuratie met de naam ' ISVBoxConfig. isvbox ' die op de pull-server wordt geplaatst. De naam van de knooppunt configuratie is gemaakt als "configuratiepad. knooppunt naam".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Stap 5: de meta gegevens van het pakket maken en onderhouden

Voor elk pakket dat u in de opslag plaats van het pakket plaatst, hebt u een nuspec nodig waarin het wordt beschreven.
Dat nuspec moet worden gecompileerd en opgeslagen op uw NuGet-server. Dit proces wordt [hier](https://docs.nuget.org/create/creating-and-publishing-a-package)beschreven. U kunt MyGet.org gebruiken als een NuGet-server. Deze service wordt verkocht, maar er is een gratis start-SKU. Op NuGet.org vindt u instructies voor het installeren van uw eigen NuGet-server voor uw privé-pakketten.

## <a name="step-6-tying-it-all-together"></a>Stap 6: alles koppelen

Telkens wanneer een versie QA gaat en is goedgekeurd voor implementatie, wordt het pakket gemaakt, nuspec en nupkg bijgewerkt en geïmplementeerd op de NuGet-server. Daarnaast moet de configuratie (stap 4 hierboven) worden bijgewerkt zodat deze overeenkomt met het nieuwe versie nummer. Het moet worden verzonden naar de pull-server en gecompileerd.
Vanaf dat moment is het tot de virtuele machines die afhankelijk zijn van die configuratie om de update te halen en te installeren. Elk van deze updates is eenvoudig: slechts een regel of twee van Power shell. In het geval van een Azure-DevOps worden sommige van deze ingekapseld in bouw taken die kunnen worden samengevoegd in een build. In dit [artikel](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) vindt u meer informatie. In deze [github opslag plaats](https://github.com/Microsoft/vso-agent-tasks) worden de verschillende beschik bare build-taken beschreven.

## <a name="notes"></a>Opmerkingen

Dit gebruiks voorbeeld begint met een virtuele machine van een algemene Windows Server 2012 R2-installatie kopie uit de Azure-galerie. U kunt beginnen met een opgeslagen installatie kopie en vervolgens van daaruit verfijnen met de DSC-configuratie.
Het wijzigen van de configuratie die in een installatie kopie wordt geïntegreerde, is echter veel moeilijker dan het dynamisch bijwerken van de configuratie met behulp van DSC.

U hoeft geen Resource Manager-sjabloon en de VM-extensie te gebruiken om deze techniek met uw Vm's te gebruiken. En uw Vm's hoeven zich niet in azure te bezien onder CD-beheer. Dat is alles wat nodig is dat chocolade wordt geïnstalleerd en de LCM die is geconfigureerd op de virtuele machine, zodat u weet waar de pull-server zich bevindt.

Wanneer u echter een pakket bijwerkt op een virtuele machine die in productie is, moet u de virtuele machine uit de rotatie halen terwijl de update wordt geïnstalleerd. Hoe u dit doet, varieert aanzienlijk. Als u bijvoorbeeld een virtuele machine achter een Azure Load Balancer, kunt u een aangepaste test toevoegen. Laat tijdens het bijwerken van de virtuele machine het eind punt een 400 retour neren. De verfijning die nodig is om deze wijziging te veroorzaken, kan zich in uw configuratie bevindt, evenals de verfijning om deze terug te zetten om een 200 te retour neren nadat de update is voltooid.

De volledige bron voor dit gebruiks voorbeeld bevindt zich in [dit Visual Studio-project](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) op github.

## <a name="related-articles"></a>Verwante artikelen
* [Overzicht van Azure Automation DSC](automation-dsc-overview.md)
* [Azure Automation DSC-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Onboarding van machines voor beheer door Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Automation State Configuration](automation-dsc-overview.md) (Engelstalig) voor een overzicht
- Zie aan de slag [met de configuratie van de Azure Automation-status](automation-dsc-getting-started.md) om aan de slag te gaan.
- Zie [configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md) voor meer informatie over het compileren van DSC-configuraties zodat u ze aan doel knooppunten kunt toewijzen.
- Zie [Azure Automation status configuratie-cmdlets](/powershell/module/azurerm.automation/#automation) voor informatie over de Power shell-cmdlet.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/) voor prijs informatie.
- Voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie gaat u naar [continue implementatie met behulp van Azure Automation-status configuratie en chocolade](automation-dsc-cd-chocolatey.md)
