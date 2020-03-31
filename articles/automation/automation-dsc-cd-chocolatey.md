---
title: Azure Automation State Configuration Continue Deployment with Chocolatey Azure Automation State Configuration Continue Deployment with Chocolatey Azure Automation
description: DevOps continue implementatie met Azure Automation State Configuration, DSC en Chocolatey package manager.  Voorbeeld met de volledige JSON Resource Manager-sjabloon en PowerShell-bron.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 4445f6e9b72380b66f3282d50871b4283f7fc7fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966745"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Gebruiksvoorbeeld: continue implementatie naar virtuele machines met configuratie van automatiseringsstatus en chocolatey

In een DevOps-wereld zijn er veel tools om te helpen met verschillende punten in de Continuous Integration-pijplijn. Azure Automation State Configuration is een welkome nieuwe toevoeging aan de opties die DevOps-teams kunnen gebruiken. In dit artikel wordt het instellen van continue implementatie (CD) voor een Windows-computer getoond. U de techniek eenvoudig uitbreiden om zoveel Windows-computers op te nemen als nodig is in de rol (bijvoorbeeld een website) en van daaruit ook naar extra rollen.

![Continue implementatie voor IaaS VM's](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Op een hoog niveau

Er is nogal wat aan de hand hier, maar gelukkig kan worden opgesplitst in twee belangrijke processen:

- Code schrijven en testen, vervolgens installatiepakketten maken en publiceren voor grote en secundaire versies van het systeem.
- Vm's maken en beheren die de code in de pakketten installeren en uitvoeren.  

Zodra beide kernprocessen zijn geïmplementeerd, is het een korte stap om het pakket dat op een bepaalde VM wordt uitgevoerd automatisch bij te werken wanneer nieuwe versies worden gemaakt en geïmplementeerd.

## <a name="component-overview"></a>Overzicht van onderdelen

Package managers zoals [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) zijn vrij bekend in de Linux wereld, maar niet zozeer in de Windows-wereld.
[Chocolatey](https://chocolatey.org/) is zo'n ding, en Scott Hanselman's [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) over het onderwerp is een geweldige intro. In een notendop, Chocolatey u pakketten installeren van een centrale opslagplaats van pakketten in een Windows-systeem met behulp van de command line. U uw eigen opslagplaats maken en beheren en Chocolatey kan pakketten installeren van een willekeurig aantal opslagplaatsen die u aanwijst.

Desired State Configuration (DSC)[(overzicht)](/powershell/scripting/dsc/overview/overview)is een PowerShell-tool waarmee u de gewenste configuratie voor een machine declareren. U bijvoorbeeld zeggen: Ik wil dat Chocolatey wordt geïnstalleerd, ik wil iis-geïnstalleerd, ik wil poort 80 openen, ik wil versie 1.0.0 van mijn website installeren. De DSC Local Configuration Manager (LCM) implementeert die configuratie. Een DSC Pull Server bevat een opslagplaats van configuraties voor uw machines. De LCM op elke machine controleert regelmatig om te zien of de configuratie overeenkomt met de opgeslagen configuratie. Het kan de status rapporteren of proberen de machine weer in lijn te brengen met de opgeslagen configuratie. U de opgeslagen configuratie op de pull-server bewerken om ervoor te zorgen dat een machine of set machines in overeenstemming komt met de gewijzigde configuratie.

Azure Automation is een beheerde service in Microsoft Azure waarmee u verschillende taken automatiseren met runbooks, knooppunten, referenties, resources en assets, zoals schema's en globale variabelen.
Azure Automation State Configuration breidt deze automatiseringsmogelijkheden uit met PowerShell DSC-hulpprogramma's. Hier is een geweldig [overzicht.](automation-dsc-overview.md)

Een DSC-bron is een codemodule met specifieke mogelijkheden, zoals het beheren van netwerken, Active Directory of SQL Server. De Chocolatey DSC Resource weet hoe je toegang krijgt tot een NuGet Server (onder andere), pakketten downloaden, pakketten installeren, enzovoort. Er zijn veel andere DSC-bronnen in de [PowerShell Gallery.](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)
Deze modules worden geïnstalleerd in uw Azure Automation State Configuration Pull Server (door u) zodat ze door uw configuraties kunnen worden gebruikt.

Resource Manager-sjablonen bieden een declaratieve manier om uw infrastructuur te genereren - zaken als netwerken, subnetten, netwerkbeveiliging en routering, load balancers, NIC's, VM's, enzovoort. Hier is een [artikel](../azure-resource-manager/management/deployment-models.md) dat het Resource Manager-implementatiemodel (declaratief) vergelijkt met het Azure Service Management (ASM of klassiek) implementatiemodel (noodzakelijk) en waarin de belangrijkste resourceproviders, compute, storage en netwerk worden besproken.

Een belangrijk kenmerk van een Resource Manager-sjabloon is de mogelijkheid om een VM-extensie in de VM te installeren terwijl deze is ingericht. Een VM-extensie heeft specifieke mogelijkheden, zoals het uitvoeren van een aangepast script, het installeren van antivirussoftware of het uitvoeren van een DSC-configuratiescript. Er zijn vele andere soorten VM-extensies.

## <a name="quick-trip-around-the-diagram"></a>Snelle reis rond het diagram

Vanaf de bovenkant schrijf je je code, bouw en test je en maak je een installatiepakket.
Chocolatey kan omgaan met verschillende soorten installatiepakketten, zoals MSI, MSU, ZIP. En je hebt de volledige kracht van PowerShell om de werkelijke installatie te doen als Chocolateys native mogelijkheden zijn niet helemaal tot. Zet het pakket op een plaats bereikbaar - een pakket repository. In dit gebruiksvoorbeeld wordt een openbare map gebruikt in een Azure Blob-opslagaccount, maar deze kan overal zijn. Chocolatey werkt native met NuGet servers en een paar anderen voor het beheer van pakket metadata. [In dit artikel](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) worden de opties beschreven. In dit gebruiksvoorbeeld wordt NuGet gebruikt. Een Nuspec is metadata over uw pakketten. De Nuspec's worden "gecompileerd" in NuPkg's en opgeslagen in een NuGet server. Wanneer uw configuratie een pakket op naam aanvraagt en verwijst naar een NuGet-server, pakt de Chocolatey DSC Resource (nu op de VM) het pakket en installeert het voor u. U ook een specifieke versie van een pakket aanvragen.

In het linkerbenedengedeelte van de afbeelding bevindt zich een Azure Resource Manager-sjabloon. In dit gebruiksvoorbeeld registreert de VM-extensie de VM met de Azure Automation State Configuration Pull Server (dat wil zeggen een pull-server) als een knooppunt. De configuratie wordt opgeslagen in de pull-server.
Eigenlijk is het twee keer opgeslagen: een keer als platte tekst en eenmaal gecompileerd als een MOF-bestand (voor degenen die weten over dergelijke dingen.) In de portal is de MOF een "knooppuntconfiguratie" (in tegenstelling tot gewoon "configuratie"). Het is het artefact dat is gekoppeld aan een knooppunt, zodat het knooppunt de configuratie kent. Details hieronder laten zien hoe u de knooppuntconfiguratie aan het knooppunt toewijzen.

Vermoedelijk bent u al doet het beetje aan de top, of het grootste deel ervan. Het maken van de nuspec, het samenstellen en opslaan in een NuGet server is een klein ding. En je beheert al VM's. Het nemen van de volgende stap naar continue implementatie vereist het instellen van de pull-server (eenmaal), het registreren van uw knooppunten met het (een keer), en het maken en opslaan van de configuratie daar (in eerste instantie). Als pakketten vervolgens worden geüpgraded en geïmplementeerd naar de repository, vernieuwt u de configuratie- en knooppuntconfiguratie in de pull-server (indien nodig herhalen).

Als u niet begint met een resourcemanagersjabloon, is dat ook OK. Er zijn PowerShell-cmdlets die zijn ontworpen om u te helpen uw VM's te registreren met de pull-server en al de rest. Zie voor meer informatie dit artikel: [Onboarding-machines voor beheer door Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Stap 1: Het pull-server- en automatiseringsaccount instellen

Op een geverifieerde`Connect-AzureRmAccount`( ) PowerShell opdrachtregel: (kan een paar minuten duren terwijl de pull-server is ingesteld)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

U uw automatiseringsaccount in een van de volgende regio's (ook bekend als locatie): Oost-VS 2, Zuid-Centraal VS, US Gov Virginia, West-Europa, Zuidoost-Azië, Japan East, Centraal-India en Australië Zuidoost, Canada Central, Noord-Europa.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Stap 2: VM-extensietweaks voor de sjabloon ResourceManager

Details voor VM-registratie (met behulp van de PowerShell DSC VM-extensie) die is opgegeven in deze [Azure Quickstart-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Met deze stap registreert u uw nieuwe VM met de pull-server in de lijst met statusconfiguratieknooppunten. Een deel van deze registratie is het opgeven van de knooppuntconfiguratie die moet worden toegepast op het knooppunt. Deze node configuratie hoeft nog niet te bestaan in de pull-server, dus het is OK dat stap 4 is waar dit wordt gedaan voor de eerste keer. Maar hier in stap 2 moet je wel de naam van het knooppunt en de naam van de configuratie hebben bepaald. In dit gebruiksvoorbeeld is het knooppunt 'isvbox' en is de configuratie 'ISVBoxConfig'. De naam van de nodeconfiguratie (die moet worden opgegeven in DeploymentTemplate.json) is dus 'ISVBoxConfig.isvbox'.

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Stap 3: Vereiste DSC-resources toevoegen aan de pull-server

De PowerShell Gallery is ingeschakeld om DSC-resources te installeren in uw Azure Automation-account.
Navigeer naar de gewenste resource en klik op de knop 'Implementeren naar Azure-automatisering'.

![Voorbeeld van PowerShell Gallery](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Een andere techniek die onlangs aan de Azure Portal is toegevoegd, stelt u in staat om nieuwe modules binnen te halen of bestaande modules bij te werken. Klik door de resource Automatiseringsaccount, de tegel Activa en ten slotte de tegel Modules. Met het pictogram Bladeren in de galerij u de lijst met modules in de galerie bekijken, inzoomen op details en uiteindelijk importeren in uw automatiseringsaccount. Dit is een geweldige manier om uw modules van tijd tot tijd up-to-date te houden. En de importfunctie controleert afhankelijkheden met andere modules om ervoor te zorgen dat niets uit de pas loopt.

Of, er is de handmatige aanpak. De mapstructuur van een PowerShell-integratiemodule voor een Windows-computer is iets anders dan de mapstructuur die wordt verwacht door de Azure Automation.
Dit vereist een beetje tweaken van uw kant. Maar het is niet moeilijk, en het is slechts eenmaal per resource gedaan (tenzij u wilt upgraden in de toekomst.) Zie dit artikel: [Integratiemodules voor Azure Automation voor](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/) meer informatie over het ontwerpen van PowerShell-integratiemodules

- Installeer de module die u nodig hebt op uw werkstation, als volgt:
  - [Windows Management Framework, v5 installeren](https://aka.ms/wmf5latest) (niet nodig voor Windows 10)
  - `Install-Module –Name MODULE-NAME`<- pakt de module uit de PowerShell Gallery
- De modulemap `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` kopiëren naar een tijdelijke map
- Voorbeelden en documentatie uit de hoofdmap verwijderen
- Rits de hoofdmap vast en noem het ZIP-bestand precies hetzelfde als de map 
- Plaats het ZIP-bestand op een bereikbare HTTP-locatie, zoals blob-opslag in een Azure Storage-account.
- Voer deze PowerShell uit:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

Het meegeleverde voorbeeld voert deze stappen uit voor cChoco en xNetworking. Zie de [Notes](#notes) for special handling voor cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Stap 4: De knooppuntconfiguratie toevoegen aan de pull-server

Er is niets bijzonders aan de eerste keer dat u uw configuratie importeert in de pull-server en compileert. Alle volgende import/compiles van dezelfde configuratie zien er precies hetzelfde uit. Elke keer dat u uw pakket bijwerkt en het naar de productie moet pushen, doet u deze stap nadat u ervoor hebt gezorgd dat het configuratiebestand correct is , inclusief de nieuwe versie van uw pakket. Hier is het configuratiebestand en PowerShell:

ISVBoxConfig.ps1:

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

Nieuw-ConfigurationScript.ps1:

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

Deze stappen resulteren in een nieuwe node configuratie genaamd "ISVBoxConfig.isvbox" wordt geplaatst op de pull-server. De naam van de nodeconfiguratie is gemaakt als "configurationName.nodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Stap 5: Metagegevens van pakketten maken en onderhouden

Voor elk pakket dat u in de pakketopslagplaats plaatst, hebt u een nuspec nodig die het beschrijft.
Die nuspec moet worden gecompileerd en opgeslagen in je NuGet-server. Dit proces wordt [hier](https://docs.nuget.org/create/creating-and-publishing-a-package)beschreven. Je MyGet.org gebruiken als NuGet-server. Ze verkopen deze dienst, maar hebben een starter SKU die gratis is. Bij NuGet.org vindt u instructies voor het installeren van uw eigen NuGet-server voor uw privépakketten.

## <a name="step-6-tying-it-all-together"></a>Stap 6: Alles aan elkaar knopen

Elke keer dat een versie QA passeert en is goedgekeurd voor implementatie, wordt het pakket gemaakt, nuspec en nupkg bijgewerkt en geïmplementeerd op de NuGet-server. Bovendien moet de configuratie (stap 4 hierboven) worden bijgewerkt om akkoord te gaan met het nieuwe versienummer. Het moet worden verzonden naar de pull-server en gecompileerd.
Vanaf dat moment is het aan de VM's die afhankelijk zijn van die configuratie om de update te trekken en te installeren. Elk van deze updates zijn eenvoudig - slechts een lijn of twee van PowerShell. In het geval van Azure DevOps zijn sommige van hen ingekapseld in buildtaken die kunnen worden samengevoegd in een build. Dit [artikel](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) geeft meer details. Deze [GitHub repo](https://github.com/Microsoft/vso-agent-tasks) beschrijft de verschillende beschikbare buildtaken.

## <a name="notes"></a>Opmerkingen

Dit gebruiksvoorbeeld begint met een VM uit een algemene Windows Server 2012 R2-afbeelding uit de Azure-galerie. U vanaf elke opgeslagen afbeelding beginnen en vervolgens vanaf daar tweaken met de DSC-configuratie.
Het wijzigen van de configuratie die in een afbeelding wordt gebakken, is echter veel moeilijker dan het dynamisch bijwerken van de configuratie met DSC.

U hoeft geen Resource Manager-sjabloon en de VM-extensie te gebruiken om deze techniek met uw VM's te gebruiken. En uw VM's hoeven niet op Azure te staan om onder cd-beheer te staan. Het enige dat nodig is, is dat Chocolatey wordt geïnstalleerd en de LCM geconfigureerd op de VM, zodat het weet waar de pull-server is.

Natuurlijk, wanneer u een pakket bijwerkt op een VM die in productie is, moet u die VM uit de rotatie halen terwijl de update is geïnstalleerd. Hoe je dit doet varieert sterk. Met een VM achter een Azure Load Balancer u bijvoorbeeld een aangepaste sonde toevoegen. Laat het eindpunt van de sonde tijdens het bijwerken van de VM een 400 retourneren. De tweak die nodig is om deze verandering te veroorzaken kan worden binnen uw configuratie, net als de tweak om het terug te schakelen naar het retourneren van een 200 zodra de update is voltooid.

Volledige bron voor dit gebruiksvoorbeeld is in [dit Visual Studio-project](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) op GitHub.

## <a name="related-articles"></a>Verwante artikelen
* [Overzicht van Azure Automation DSC](automation-dsc-overview.md)
* [Azure Automation DSC-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Onboarding machines voor beheer door Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Automation State Configuration](automation-dsc-overview.md) voor een overzicht
- Zie [Aan de slag met Azure Automation State Configuration](automation-dsc-getting-started.md) om aan de slag te gaan.
- Zie [Configuraties compileren in Azure Automation State Configuration](automation-dsc-compile.md) voor meer informatie over het samenstellen van DSC-configuraties, zodat u ze toewijzen aan doelknooppunten.
- Zie [Cmdlets Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) voor PowerShell-cmdlet
- Zie Azure [Automation State Configuration Pricing](https://azure.microsoft.com/pricing/details/automation/) voor prijsinformatie
- Zie [Continue implementatie met Azure Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md) voor een voorbeeld van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.
