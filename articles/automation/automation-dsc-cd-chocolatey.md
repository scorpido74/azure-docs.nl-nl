---
title: Azure Automation State Configuration continuous deployment with Chocolatey
description: Beschrijft de continue implementatie van DevOps met azure automation-statusconfiguratie met de Chocolatey-pakketbeheerder. Bevat een voorbeeld met volledige JSON Resource Manager-sjabloon en PowerShell-bron.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 706ab128af4379a56223ff65fb12f29d37b524f7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383264"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Continue implementatie naar virtuele machines bieden met configuratie van automatiseringsstatus en chocolatey

In een DevOps-wereld zijn er veel tools om te helpen met verschillende punten in de continue integratiepijplijn. Azure Automation [State Configuration](automation-dsc-overview.md) is een welkome nieuwe toevoeging aan de opties die DevOps-teams kunnen gebruiken. 

Azure Automation is een beheerde service in Microsoft Azure waarmee u verschillende taken automatiseren met runbooks, knooppunten en gedeelde bronnen, zoals referenties, planningen en globale variabelen. Azure Automation State Configuration breidt deze automatiseringsmogelijkheden uit met PowerShell Desired State Configuration (DSC)-hulpprogramma's. Hier is een geweldig [overzicht.](automation-dsc-overview.md)

In dit artikel wordt uitgelegd hoe u cd's (Continue implementatie) instelt voor een Windows-computer. U de techniek eenvoudig uitbreiden om zoveel Windows-computers op te nemen als nodig is in de rol, bijvoorbeeld een website, en van daaruit naar extra rollen gaan.

![Continue implementatie voor IaaS VM's](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="at-a-high-level"></a>Op een hoog niveau

Er is nogal wat aan de hand hier, maar gelukkig kan worden opgesplitst in twee belangrijke processen:

- Code schrijven en testen, vervolgens installatiepakketten maken en publiceren voor grote en secundaire versies van het systeem.
- Vm's maken en beheren die de code in de pakketten installeren en uitvoeren.  

Zodra beide kernprocessen zijn geïmplementeerd, is het eenvoudig om het pakket op uw VM's automatisch bij te werken wanneer nieuwe versies worden gemaakt en geïmplementeerd.

## <a name="component-overview"></a>Overzicht van onderdelen

Package managers zoals [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) zijn bekend in de Linux wereld, maar niet zozeer in de Windows-wereld.
[Chocolatey](https://chocolatey.org/) is zo'n ding, en Scott Hanselman's [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) over het onderwerp is een geweldige introductie. In een notendop, Chocolatey u de opdrachtregel gebruiken om pakketten te installeren vanuit een centrale opslagplaats op een Windows-besturingssysteem. U uw eigen opslagplaats maken en beheren en Chocolatey kan pakketten installeren van een willekeurig aantal opslagplaatsen die u aanwijst.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) is een PowerShell-tool waarmee u de gewenste configuratie voor een machine declareren. Als u bijvoorbeeld wilt dat Chocolatey wordt geïnstalleerd, IIS is geïnstalleerd, poort 80 is geopend en versie 1.0.0 van uw website wilt installeren, implementeert de DSC Local Configuration Manager (LCM) die configuratie. Een DSC pull-server bevat een opslagplaats met configuraties voor uw machines. De LCM op elke machine controleert regelmatig om te zien of de configuratie overeenkomt met de opgeslagen configuratie. Het kan de status rapporteren of proberen de machine weer in lijn te brengen met de opgeslagen configuratie. U de opgeslagen configuratie op de pull-server bewerken om ervoor te zorgen dat een machine of set machines in overeenstemming komt met de gewijzigde configuratie.

Een DSC-bron is een codemodule met specifieke mogelijkheden, zoals het beheren van netwerken, Active Directory of SQL Server. De Chocolatey DSC Resource weet hoe je toegang krijgt tot een NuGet Server (onder andere), pakketten downloaden, pakketten installeren, enzovoort. Er zijn veel andere DSC-bronnen in de [PowerShell Gallery.](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title) U installeert deze modules op uw Azure Automation State Configuration pull-server voor gebruik door uw configuraties.

Resource Manager-sjablonen bieden een declaratieve manier om uw infrastructuur te genereren, bijvoorbeeld netwerken, subnetten, netwerkbeveiliging en routering, load balancers, NIC's, VM's, enzovoort. Hier is een [artikel](../azure-resource-manager/management/deployment-models.md) dat het Resource Manager-implementatiemodel (declaratief) vergelijkt met het Azure Service Management (ASM of klassiek) implementatiemodel (noodzakelijk). Dit artikel bevat een bespreking van de belangrijkste resourceproviders: compute, storage en netwerk.

Een belangrijk kenmerk van een Resource Manager-sjabloon is de mogelijkheid om een VM-extensie in de VM te installeren terwijl deze is ingericht. Een VM-extensie heeft specifieke mogelijkheden, zoals het uitvoeren van een aangepast script, het installeren van antivirussoftware en het uitvoeren van een DSC-configuratiescript. Er zijn vele andere soorten VM-extensies.

## <a name="quick-trip-around-the-diagram"></a>Snelle reis rond het diagram

Vanaf de bovenkant schrijf je je code, bouw je deze, test je deze en maak je een installatiepakket. Chocolatey kan omgaan met verschillende soorten installatiepakketten, zoals MSI, MSU, ZIP. En je hebt de volledige kracht van PowerShell om de werkelijke installatie te doen als Chocolatey's native mogelijkheden niet aan de beurt zijn. Zet het pakket op een plaats bereikbaar - een pakket repository. In dit gebruiksvoorbeeld wordt een openbare map gebruikt in een Azure Blob-opslagaccount, maar deze kan overal zijn. Chocolatey werkt native met NuGet servers en een paar anderen voor het beheer van pakket metadata. [In dit artikel](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) worden de opties beschreven. In het gebruiksvoorbeeld wordt NuGet gebruikt. Een Nuspec is metadata over uw pakketten. De Nuspec-informatie wordt gecompileerd in een NuPkg en opgeslagen op een NuGet-server. Wanneer uw configuratie een pakket op naam aanvraagt en verwijst naar een NuGet-server, grijpt de Chocolatey DSC-bron op de VM het pakket en installeert het. U ook een specifieke versie van een pakket aanvragen.

Linksonder in de afbeelding staat een Azure Resource Manager-sjabloon. In dit gebruiksvoorbeeld registreert de VM-extensie de VM met de Pull-server Azure Automation State Configuration als knooppunt. De configuratie wordt twee keer opgeslagen in de pull-server: één keer als platte tekst en eenmaal gecompileerd als MOF-bestand. In de Azure-portal vertegenwoordigt de MOF een knooppuntconfiguratie, in tegenstelling tot een eenvoudige configuratie. Het is het artefact dat is gekoppeld aan een knooppunt, zodat het knooppunt de configuratie kent. Details hieronder laten zien hoe u de knooppuntconfiguratie aan het knooppunt toewijzen.

Het maken van de Nuspec, het samenstellen en opslaan in een NuGet-server is een klein ding. En je beheert al VM's. 

Als u de volgende stap naar continue implementatie neemt, moet u de pull-server één keer instellen, uw knooppunten één keer registreren en de eerste configuratie op de server maken en opslaan. Aangezien pakketten worden geüpgraded en geïmplementeerd naar de repository, hoeft u alleen de configuratie en knooppuntconfiguratie op de pull-server te vernieuwen als dat nodig is.

Als u niet begint met een resourcemanagersjabloon, is dat prima. Er zijn PowerShell-opdrachten waarmee u uw VM's registreren bij de pull-server. Zie [Onboarding-machines voor beheer door Azure Automation State Configuration voor](automation-dsc-onboarding.md)meer informatie.

## <a name="about-the-usage-example"></a>Over het gebruiksvoorbeeld

Het gebruiksvoorbeeld in dit artikel begint met een VM van een algemene Windows Server 2012 R2-afbeelding uit de Azure-galerie. U vanaf elke opgeslagen afbeelding beginnen en vervolgens vanaf daar tweaken met de DSC-configuratie.
Het wijzigen van de configuratie die in een afbeelding wordt gebakken, is echter veel moeilijker dan het dynamisch bijwerken van de configuratie met DSC.

U hoeft geen Resource Manager-sjabloon en de VM-extensie te gebruiken om deze techniek met uw VM's te gebruiken. En uw VM's hoeven niet op Azure te staan om onder cd-beheer te staan. Het enige dat nodig is, is dat Chocolatey wordt geïnstalleerd en de LCM geconfigureerd op de VM, zodat het weet waar de pull-server is.

Wanneer u een pakket bijwerkt op een vm die in productie is, moet u die vm uit de rotatie halen terwijl de update is geïnstalleerd. Hoe je dit doet varieert sterk. Met een VM achter een Azure Load Balancer u bijvoorbeeld een aangepaste sonde toevoegen. Laat het eindpunt van de sonde tijdens het bijwerken van de VM een 400 retourneren. De tweak die nodig is om deze verandering te veroorzaken kan worden binnen uw configuratie, net als de tweak om het terug te schakelen naar het retourneren van een 200 zodra de update is voltooid.

Volledige bron voor dit gebruiksvoorbeeld is in [dit Visual Studio-project](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) op GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Stap 1: Het pull-server- en automatiseringsaccount instellen

Op een geverifieerde`Connect-AzAccount`( ) PowerShell opdrachtregel: (kan een paar minuten duren terwijl de pull-server is ingesteld)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

U uw Automation-account in een van de volgende regio's (ook wel locaties genoemd): Oost-VS 2, Zuid-Centraal VS, US Gov Virginia, West-Europa, Zuidoost-Azië, Japan-Oost, Centraal-India en Australië Zuidoost, Canada Centraal, Noord-Europa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Stap 2: Vm-extensietweaks maken voor de sjabloon ResourceManager

Details voor VM-registratie (met behulp van de PowerShell DSC VM-extensie) die is opgegeven in deze [Azure Quickstart-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Met deze stap registreert u uw nieuwe VM met de pull-server in de lijst met statusconfiguratieknooppunten. Een deel van deze registratie is het opgeven van de knooppuntconfiguratie die moet worden toegepast op het knooppunt. Deze node configuratie hoeft nog niet te bestaan in de pull server, dus het is prima dat stap 4 is waar dit wordt gedaan voor de eerste keer. Maar hier in stap 2 moet je wel de naam van het knooppunt en de naam van de configuratie hebben bepaald. In dit gebruiksvoorbeeld is het knooppunt 'isvbox' en is de configuratie 'ISVBoxConfig'. De naam van de nodeconfiguratie (die moet worden opgegeven in DeploymentTemplate.json) is dus 'ISVBoxConfig.isvbox'.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Stap 3: Vereiste DSC-resources toevoegen aan de pull-server

De PowerShell Gallery is ingeschakeld om DSC-resources te installeren in uw Azure Automation-account.
Navigeer naar de gewenste resource en klik op de knop 'Implementeren naar Azure-automatisering'.

![Voorbeeld van PowerShell Gallery](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Een andere techniek die onlangs aan de Azure-portal is toegevoegd, stelt u in staat om nieuwe modules binnen te halen of bestaande modules bij te werken. Klik door de bron van het automatiseringsaccount, de tegel Activa en ten slotte de tegel Modules. Met het pictogram Bladeren in de galerie u de lijst met modules in de galerie bekijken, inzoomen op details en uiteindelijk importeren in uw Automatiseringsaccount. Dit is een geweldige manier om uw modules van tijd tot tijd up-to-date te houden. En de importfunctie controleert afhankelijkheden met andere modules om ervoor te zorgen dat niets uit de pas loopt.

Of, er is de handmatige aanpak. Deze benadering wordt slechts één keer per resource gebruikt, tenzij u deze later wilt upgraden. Zie [Integratiemodules voor Azure Automation voor](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)meer informatie over het ontwerpen van PowerShell-integratiemodules.

>[!NOTE]
>De mapstructuur van een PowerShell-integratiemodule voor een Windows-computer is iets anders dan de mapstructuur die wordt verwacht door de Azure Automation. 

1. Installeer [Windows Management Framework v5](https://aka.ms/wmf5latest) (niet nodig voor Windows 10).

2. Installeer de integratiemodule.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Kopieer de modulemap van **c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** naar een tijdelijke map.

4. Verwijder voorbeelden en documentatie uit de hoofdmap.

5. Rits de hoofdmap dicht en geef het ZIP-bestand een naam met de naam van de map.

6. Plaats het ZIP-bestand op een bereikbare HTTP-locatie, zoals blob-opslag in een Azure Storage-account.

7. Voer de volgende opdracht uit.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

Het meegeleverde voorbeeld implementeert deze stappen voor cChoco en xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Stap 4: De knooppuntconfiguratie toevoegen aan de pull-server

Er is niets bijzonders aan de eerste keer dat u uw configuratie importeert in de pull-server en compileert. Alle latere importen of compilaties van dezelfde configuratie zien er precies hetzelfde uit. Elke keer dat u uw pakket bijwerkt en het naar de productie moet pushen, doet u deze stap nadat u ervoor hebt gezorgd dat het configuratiebestand correct is , inclusief de nieuwe versie van uw pakket. Hier is het configuratiebestand **ISVBoxConfig.ps1:**

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

Hier is het **script Nieuw-ConfigurationScript.ps1** (aangepast om de Az-module te gebruiken):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Deze stappen resulteren in een nieuwe node configuratie genaamd **ISVBoxConfig.isvbox** wordt geplaatst op de pull-server. De naam van de `configurationName.nodeName`nodeconfiguratie is gebouwd als .

## <a name="step-5-create-and-maintain-package-metadata"></a>Stap 5: Metagegevens van pakketten maken en onderhouden

Voor elk pakket dat u in de pakketopslagplaats plaatst, hebt u een nuspec nodig die het beschrijft.
Die nuspec moet worden gecompileerd en opgeslagen in je NuGet-server. Dit proces wordt [hier](https://docs.nuget.org/create/creating-and-publishing-a-package)beschreven. Je MyGet.org gebruiken als NuGet-server. Ze verkopen deze dienst, maar hebben een starter SKU die gratis is. Bij NuGet.org vindt u instructies voor het installeren van uw eigen NuGet-server voor uw privépakketten.

## <a name="step-6-tie-it-all-together"></a>Stap 6: Bind alles bij elkaar

Elke keer dat een versie QA passeert en is goedgekeurd voor implementatie, wordt het pakket gemaakt en worden nuspec en nupkg bijgewerkt en geïmplementeerd op de NuGet-server. De configuratie (stap 4 hierboven) moet ook worden bijgewerkt om akkoord te gaan met het nieuwe versienummer. Het moet vervolgens worden verzonden naar de pull-server en gecompileerd.

Vanaf dat moment is het aan de VM's die afhankelijk zijn van die configuratie om de update te trekken en te installeren. Elk van deze updates is eenvoudig - slechts een lijn of twee van PowerShell. Voor Azure DevOps zijn sommige van deze ingebouwde buildtaken die kunnen worden geketend in een build. Dit [artikel](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) geeft meer details. Deze [GitHub repo](https://github.com/Microsoft/vso-agent-tasks) beschrijft de beschikbare buildtaken.

## <a name="related-articles"></a>Verwante artikelen:
* [Overzicht van Azure Automation DSC](automation-dsc-overview.md)
* [Azure Automation DSC-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Onboarding machines voor beheer door Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Automation State Configuration](automation-dsc-overview.md)voor een overzicht.
- Zie [Aan de slag met Azure Automation State Configuration](automation-dsc-getting-started.md)om aan de slag te gaan.
- Zie [Configuraties compileren in Azure Automation State Configuration](automation-dsc-compile.md)voor meer informatie over het samenstellen van DSC-configuraties, zodat u ze toewijzen aan doelknooppunten.
- Zie [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)voor een PowerShell-cmdletreferentie.
- Zie Azure Automation State Configuration Pricing voor prijsinformatie voor [prijsinformatie.](https://azure.microsoft.com/pricing/details/automation/)
- Zie [Continue implementatie met Azure Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md)voor een voorbeeld van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.
