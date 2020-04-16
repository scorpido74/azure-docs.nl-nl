---
title: Boordmachines voor beheer door Azure Automation State Configuration
description: In dit artikel wordt beschreven hoe u machines instelt voor beheer met Azure Automation State Configuration.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 04/15/2020
manager: carmonm
ms.openlocfilehash: 4fc9f701f4f832deb1fed34d7ac9f888521d3830
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406203"
---
# <a name="onboard-machines-for-management-by-azure-automation-state-configuration"></a>Boordmachines voor beheer door Azure Automation State Configuration

Azure Automation State Configuration is a configuration management service for Desired State Configuration (DSC) nodes in cloud or on-premises datacenters. U hebt toegang tot de service in de Azure-portal door **statusconfiguratie (DSC)** te selecteren onder **Configuratiebeheer**. 

Met Azure Automation State Configuration u snel en eenvoudig schaalbaarheid bereiken voor duizenden machines vanaf een centrale, veilige locatie. U eenvoudig machines aan boord nemen, ze declaratieve configuraties toewijzen en rapporten weergeven die de naleving van de opgegeven status van elke machine weergeven.

De azure automation state configuration service is aan DSC wat Azure Automation runbooks zijn voor PowerShell-scripting. Met andere woorden, op dezelfde manier dat Azure Automation u helpt bij het beheren van PowerShell-scripts, helpt het u ook bij het beheren van DSC-configuraties. Zie Azure [Automation State Configuration Overview](automation-dsc-overview.md)voor meer informatie over de voordelen van het gebruik van Azure Automation State Configuration.

U Azure Automation State Configuration gebruiken om verschillende machines te beheren:

- Virtuele Azure-machines (VM's)
- Azure virtuele machines (klassiek)
- Fysieke/virtuele Windows-machines on-premises of in een andere cloud dan Azure (inclusief Amazon Web Services [AWS] Elastic Compute Cloud [EC2]-exemplaren)
- Fysieke/virtuele Linux-machines on-premises, in Azure of in een andere cloud dan Azure

Als u nog niet klaar bent om de machineconfiguratie vanuit de cloud te beheren, u Azure Automation State Configuration gebruiken als een eindpunt voor alleen rapport. Met deze functie u configuraties instellen of pushen via DSC en rapportagedetails bekijken in Azure Automation.

> [!NOTE]
> Azure VM's beheren met Azure Automation State Configuration is gratis inbegrepen als de geïnstalleerde Azure VM Desired State Configuration extension versie 2.70 of hoger is. Zie [Automatiseringsprijzen](https://azure.microsoft.com/pricing/details/automation/)voor meer informatie.

In de volgende secties van dit artikel wordt beschreven hoe u de eerder vermelde machines gebruiken voor azure-automatiseringsstatusconfiguratie.

## <a name="onboard-azure-vms"></a>Azure VM's aan boord

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules voor uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

Omdat de Azure-extensie asynchroon wordt uitgevoerd, bieden we stappen om de voortgang ervan bij te houden of problemen op te lossen in het gedeelte [Problemen met azure voor het onboarden](#troubleshoot-azure-virtual-machine-onboarding) van azure voor virtuele machines van dit artikel.

> [!NOTE]
> Het implementeren van DSC naar een Linux-knooppunt maakt gebruik van de */tmp-map.* Modules zoals `nxautomation` worden tijdelijk gedownload voor verificatie voordat ze worden geïnstalleerd. Om ervoor te zorgen dat modules correct zijn geïnstalleerd, heeft de Log Analytics-agent voor Linux lees-/schrijfmachtigingen nodig voor de */tmp-map.*<br><br>
> De Log Analytics-agent voor Linux wordt uitgevoerd als *de omsagent-gebruiker.* Als u schrijftoestemming wilt verlenen aan de `setfacl -m u:omsagent:rwx /tmp` *omsagent-gebruiker,* voert u de opdracht uit .

### <a name="onboard-vms-by-using-the-azure-portal"></a>Vm's aan boord met behulp van de Azure-portal

Ga als lid van Azure VM's naar Azure Automation State Configuration via de [Azure-portal:](https://portal.azure.com/)

1. Ga naar het Azure Automation-account waarin u vm's aan boord wilt nemen. 

1. Selecteer op de pagina **Statusconfiguratie** het tabblad **Knooppunten** en selecteer **Vervolgens Toevoegen**.

1. Kies een VM aan boord.

1. Als de machine niet de powershell gewenste statusextensie heeft geïnstalleerd en de energiestatus wordt uitgevoerd, selecteert u **Verbinding maken**.

1. Voer **onder Registratie**de [LCM-waarden (PowerShell DSC Local Configuration Manager) in](/powershell/scripting/dsc/managing-nodes/metaConfig) die vereist zijn voor uw use case. Optioneel u een knooppuntconfiguratie invoeren die u aan de VM wilt toewijzen.

![Het vm-registratiedeelvenster](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-vms-by-using-azure-resource-manager-templates"></a>Vm's aan boord met Azure Resource Manager-sjablonen

U een VM implementeren en aan boord nemen van azure-automatiseringsstatusconfiguratie met Azure Resource Manager-sjablonen. Zie [Server beheerd door de service Gewenste statusconfiguratie](https://azure.microsoft.com/resources/templates/101-automation-configuration/)voor een voorbeeldsjabloon die een bestaande VM aande begeeft in Azure Automation State. Als u een virtuele machineschaalset beheert, raadpleegt u de voorbeeldsjabloon in de configuratie van [de virtuele machineschaalset die wordt beheerd door Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-vms-by-using-powershell"></a>Vm's aan boord met PowerShell

U de [cmdlet Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) in PowerShell gebruiken om VM's aan boord te nemen van Azure Automation State Configuration. 

> [!NOTE]
>De `Register-AzAutomationDscNode` cmdlet wordt momenteel alleen geïmplementeerd voor machines met Windows, omdat de cmdlet alleen de Windows-extensie activeert.

### <a name="register-vms-across-azure-subscriptions"></a>VM's registreren voor Azure-abonnementen

De beste manier om VM's van andere Azure-abonnementen te registreren, is door de DSC-extensie te gebruiken in een Azure Resource Manager-implementatiesjabloon. Voorbeelden worden gegeven in [de extensie Gewenste statusconfiguratie met Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Zie de onboard [veilig met behulp van de registratiesectie](#onboard-securely-by-using-registration) in dit artikel om de registratiesleutel en registratie-URL te vinden die u als parameters in de sjabloon wilt gebruiken.

## <a name="onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Fysieke/virtuele Windows-machines aan boord of in een andere cloud dan Azure

U Windows-servers die on-premises of in andere cloudomgevingen, waaronder AWS EC2-exemplaren, worden uitgevoerd, ingebruiknemen naar Azure Automation State Configuration. De servers moeten [uitgaande toegang hebben tot Azure.](automation-dsc-overview.md#network-planning)

1. Zorg ervoor dat de nieuwste versie van [Windows Management Framework 5](https://aka.ms/wmf5latest) is geïnstalleerd op de machines die moeten worden aangesloten op azure automation state configuration. Bovendien moet Windows Management Framework 5 worden geïnstalleerd op de computer die u gebruikt voor de onboarding-bewerking.
1. Als u een map wilt maken met de vereiste DSC-metaconfiguraties, volgt u de aanwijzingen in de sectie [DSC-metaconfiguraties genereren.](#generate-dsc-metaconfigurations) 
1. Als u de PowerShell DSC-metaconfiguraties op afstand wilt toepassen op de machines die u aan boord wilt hebben, gebruikt u de volgende cmdlet: 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Als u de PowerShell DSC-metaconfiguraties niet op afstand toepassen, kopieert u de map **metaconfiguraties** naar de machines die u insuurt. Voeg vervolgens code toe om [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) lokaal op de machines aan te roepen.

1. Controleer in de Azure-portal of met behulp van cmdlets of de machines aan boord worden weergegeven als statusconfiguratieknooppunten die zijn geregistreerd in uw Azure Automation-account.

## <a name="onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>On-premises fysieke/virtuele Linux-machines aan boord of in een andere cloud dan Azure

U Linux-servers die on-premises of in andere cloudomgevingen worden uitgevoerd, inbouwen op Azure Automation State Configuration. De servers moeten [uitgaande toegang hebben tot Azure.](automation-dsc-overview.md#network-planning)

1. Zorg ervoor dat de nieuwste versie van [powershell gewenste statusconfiguratie voor Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) is geïnstalleerd op de machines die aan boord zijn van azure automation state configuration.
1. Als de [PowerShell DSC LCM-standaardinstellingen](/powershell/scripting/dsc/managing-nodes/metaConfig4) overeenkomen met uw use case en u machines aan boord wilt nemen, zodat ze zowel uit de azure automatiseringsstatusconfiguratie trekken als rapporteren, gaat u als volgt te werk:

   a. Gebruik op elke Linux-machine die aan `Register.py` boord gaat van Azure Automation State Configuration, om ze aan boord te nemen met de standaardinstellingen van PowerShell DSC Local Configuration Manager.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   b. Zie de onboard [veilig met behulp van het registratiegedeelte](#onboard-securely-by-using-registration) van dit artikel om de registratiesleutel en registratie-URL voor uw Automation-account te vinden.  
   c. Ga naar stap 4.
   
1. Als de PowerShell DSC LCM-standaardinstellingen niet overeenkomen met uw use case of als u machines wilt inschepen die alleen rapporteren aan Azure Automation State Configuration, voert u de volgende stappen a-d uit. Anders gaat u direct naar stap d.

    a. Als u een map wilt maken met de vereiste DSC-metaconfiguraties, volgt u de aanwijzingen in de sectie [DSC-metaconfiguraties genereren.](#generate-dsc-metaconfigurations)

    b. Zorg ervoor dat de nieuwste versie van [Windows Management Framework 5](https://aka.ms/wmf5latest) is geïnstalleerd op de machine die wordt gebruikt voor onboarding.

    c. Als u de PowerShell DSC-metaconfiguraties op afstand wilt toepassen op de machines die u aan boord wilt hebben, voegt u de volgende code toe:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

    d. Als u de PowerShell DSC-metaconfiguraties niet op afstand toepassen, kopieert u de metaconfiguraties die overeenkomen met de externe machines uit de map die in stap 3a is beschreven, naar de Linux-machines.

1. Voeg code toe om *Set-DscLocalConfigurationManager.py* lokaal aan te roepen op elke Linux-machine die moet worden gekoppeld aan azure automation state configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Zorg ervoor dat de machines aan boord nu worden weergegeven als DSC-knooppunten die zijn geregistreerd in uw Azure Automation-account.

## <a name="generate-dsc-metaconfigurations"></a>DSC-metaconfiguraties genereren

Als u een machine wilt aan boord van Azure Automation State Configuration, u een [DSC-metaconfiguratie](/powershell/scripting/dsc/managing-nodes/metaConfig)genereren. Met deze configuratie moet de DSC-agent uit Azure Automation State Configuration halen of rapporteren. U een DSC-metaconfiguratie voor Azure Automation State Configuration genereren met behulp van een PowerShell DSC-configuratie of de Azure Automation PowerShell-cmdlets.

> [!NOTE]
> DSC-metaconfiguraties bevatten de geheimen die u nodig hebt om een machine in te zetten bij een Automation-account voor beheer. Zorg ervoor dat u alle DSC-metaconfiguraties die u maakt, goed beschermt of deze na gebruik verwijdert.

Proxy-ondersteuning voor metaconfiguraties wordt beheerd door LCM, de Windows PowerShell DSC-engine. LCM draait op alle doelknooppunten en is verantwoordelijk voor het aanroepen van de configuratiebronnen die zijn opgenomen in een DSC-metaconfiguratiescript. 

U proxy-ondersteuning opnemen in een metaconfiguratie door definities van de `ConfigurationRepositoryWeb` `ResourceRepositoryWeb`proxy-URL en de proxy-referentie op te nemen indien nodig in de, en `ReportServerWeb` blokken. Zie [Lokaal configuratiebeheer configureren](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-by-using-a-dsc-configuration"></a>DSC-metaconfiguraties genereren met behulp van een DSC-configuratie

1. Open Visual Studio Code (of uw favoriete editor) als beheerder op een machine in uw lokale omgeving. De machine moet de nieuwste versie van [Windows Management Framework 5](https://aka.ms/wmf5latest) hebben geïnstalleerd.
1. Kopieer het volgende script lokaal. Dit script bevat een PowerShell DSC-configuratie voor het maken van metaconfiguraties en een opdracht om de metaconfiguratie-creatie af te trappen.

    > [!NOTE]
    > Namen configuratieknooppunt configuratieknooppunt zijn hoofdlettergevoelig in de Azure-portal. Als de aanvraag niet is afgestemd, wordt het knooppunt niet weergegeven onder het tabblad **Knooppunten.**

   ```powershell
   # The DSC configuration that will generate metaconfigurations.
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations.
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: Edit the following as needed for your use case.
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   DscMetaConfigs @Params
   ```

1. Vul de registratiesleutel en URL in voor uw Automatiseringsaccount, evenals de namen van de machines aan boord. Alle andere parameters zijn optioneel. Als u de registratiesleutel en registratie-URL voor uw Automatiseringsaccount wilt vinden, raadpleegt u de [onboard veilig via de registratiesectie.](#onboard-securely-by-using-registration)

1. Als u wilt dat de machines DSC-statusgegevens rapporteren aan azure automation state `ReportOnly` configuration, maar niet configuratie- of PowerShell-modules ophalen, stelt u de parameter in *op true.*

1. Als `ReportOnly` deze niet is ingesteld, rapporteren de machines DSC-statusgegevens aan Azure Automation State Configuration and pull configuration or PowerShell modules. Stel parameters dienovereenkomstig in de `ConfigurationRepositoryWeb`, `ResourceRepositoryWeb`, en `ReportServerWeb` blokken.

1. Voer het script uit. U moet nu een werkende map map genaamd *DscMetaConfigs*, die de PowerShell DSC metaconfiguraties voor de machines aan boord (als beheerder) bevat.

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-by-using-azure-automation-cmdlets"></a>DSC-metaconfiguraties genereren met Azure Automation-cmdlets

Als de PowerShell DSC LCM-standaardinstellingen overeenkomen met uw use case en u machines aan boord wilt nemen om zowel uit azure automation state configuration te trekken als te rapporteren, u de benodigde DSC-metaconfiguraties eenvoudiger genereren met behulp van de azure automation-cmdlets.

1. Open de PowerShell-console of Visual Studio Code als beheerder op een machine in uw lokale omgeving.
1. Maak verbinding met Azure Resource Manager via [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
1. Download vanuit het Automation-account waarin u knooppunten instelt de PowerShell DSC-metaconfiguraties voor de machines die u aan boord wilt gebruiken.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting.
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. U moet nu een *DscMetaConfigs-map* hebben die de PowerShell DSC-metaconfiguraties bevat voor de machines die u aan boord wilt hebben (als beheerder).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboard-securely-by-using-registration"></a>Veilig aan boord door registratie te gebruiken

Machines kunnen veilig worden aangesloten op een Azure Automation-account via het Windows Management Framework 5 DSC-registratieprotocol. Met dit protocol kan een DSC-knooppunt worden geverifieerd naar een PowerShell DSC-pull- of rapportserver, inclusief Azure Automation State Configuration. Het knooppunt registreert zich bij de server bij de registratie-URL en verifieert met behulp van een registratiesleutel. 

Tijdens de registratie onderhandelen het DSC-knooppunt en de DSC pull/report-server over een uniek certificaat voor het knooppunt dat moet worden gebruikt voor verificatie naar de server na registratie. Dit proces voorkomt dat aan boord zittende knooppunten zich voordoen als elkaar (bijvoorbeeld als een knooppunt wordt aangetast en zich kwaadwillig gedraagt). 

Na registratie wordt de registratiesleutel niet opnieuw gebruikt voor verificatie en wordt deze verwijderd uit het knooppunt.

Als u de informatie wilt ophalen die vereist is voor het configuratieregistratieprotocol van de statusconfiguratie, gaat u naar de Azure-portal en selecteert u onder **Accountinstellingen** **Toetsen**. 

![Azure-automatiseringssleutels en URL in het deelvenster Sleutels](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- **Registratie-URL**: De waarde in het **vak URL.**
- **Registratiesleutel**: de waarde in het vak **Primaire toegangssleutel** of het vak **Secundaire toegangssleutel.** Je beide toetsen gebruiken.

Voor extra beveiliging u de primaire en secundaire toegangssleutels van een Automatiseringsaccount op elk gewenst moment regenereren in het deelvenster **Sleutels.** Sleutelregeneratie voorkomt dat toekomstige knooppuntregistraties eerdere sleutels gebruiken.

## <a name="re-register-a-node"></a>Een knooppunt opnieuw registreren

Nadat u een machine hebt geregistreerd als een DSC-knooppunt in Azure Automation State Configuration, moet u dat knooppunt mogelijk opnieuw registreren om een van de volgende redenen:

- **Certificaatverlenging:** Voor versies van Windows Server die eerder zijn dan Windows Server 2019, onderhandelt elk knooppunt automatisch over een uniek certificaat voor verificatie dat na een jaar verloopt. Als een certificaat verloopt zonder verlenging, kan het knooppunt niet communiceren met Azure Automation en wordt het gemarkeerd *als Niet reageren*. 

  Momenteel kan het PowerShell DSC-registratieprotocol certificaten niet automatisch verlengen wanneer ze bijna verlopen en moet u de knooppunten na een jaar opnieuw registreren. Voordat u zich opnieuw registreert, moet u ervoor zorgen dat op elk knooppunt Windows Management Framework 5 RTM wordt uitgevoerd. 

    Herregistratie uitgevoerd 90 of minder dagen vanaf de vervaldatum van het certificaat, of op enig moment na de vervaldatum van het certificaat, resulteert in een nieuw certificaat wordt gegenereerd en gebruikt. Een oplossing voor dit probleem is opgenomen in Windows Server 2019 en hoger.

- **Wijzigingen in DSC LCM-waarden**: Mogelijk moet [u PowerShell DSC LCM-waarden](/powershell/scripting/dsc/managing-nodes/metaConfig4) wijzigen die `ConfigurationMode`zijn ingesteld tijdens de eerste registratie van het knooppunt (bijvoorbeeld ). Momenteel u deze DSC-agentwaarden alleen wijzigen door opnieuw te registreren. De enige uitzondering is de waarde knooppuntconfiguratie die aan het knooppunt is toegewezen. U deze waarde rechtstreeks wijzigen in Azure Automation DSC.

U een knooppunt opnieuw registreren, net zoals u het in eerste instantie hebt geregistreerd, met behulp van een van de onboarding-methoden die in dit document worden beschreven. U hoeft het registreren van een knooppunt niet uit te schrijven bij Azure Automation State Configuration voordat u het opnieuw registreert.

## <a name="troubleshoot-azure-virtual-machine-onboarding"></a>Problemen met azure virtual machine onboarding oplossen

Azure Automation State Configuration stelt u eenvoudig in te sluiten op Azure Windows VM's voor configuratiebeheer. Onder de motorkap wordt de Azure VM Desired State Configuration extension gebruikt om de VM te registreren met Azure Automation State Configuration. Omdat de azure VM Desired State Configuration-extensie asynchroon wordt uitgevoerd, kan het belangrijk zijn de voortgang ervan bij te houden en problemen op te lossen.

> [!NOTE]
> Het kan tot een uur duren voordat Azure Automation deze als geregistreerd heeft om een Azure Windows VM in te zetten voor azure automation in azure automation. Deze vertraging is het gevolg van de installatie van Windows Management Framework 5 op de VM door de Azure VM Desired State Configuration extension, die nodig is om de VM aan boord te nemen naar Azure Automation State Configuration.

Ga als het gaat om het oplossen of weergeven van de status van de configuratie-extensie gewenste azure vm:

1. Ga in de Azure-portal naar de VM die aan boord wordt genomen.
2. Selecteer onder **Instellingen** de optie **Extensies**. 
3. Selecteer **DSC** of **DSCForLinux,** afhankelijk van uw besturingssysteem. 
4. Selecteer **Gedetailleerde status weergeven**voor meer informatie over de extensie .

Zie [Problemen met Azure Automation Desired State Configuration (DSC) oplossen](./troubleshoot/desired-state-configuration.md)voor meer informatie over probleemoplossing.

## <a name="next-steps"></a>Volgende stappen

- Zie [Aan de slag met Azure Automation State Configuration](automation-dsc-getting-started.md)om aan de slag te gaan.
- Zie [Configuraties compileren in Azure Automation State Configuration](automation-dsc-compile.md)voor meer informatie over het samenstellen van DSC-configuraties, zodat u ze toewijzen aan doelknooppunten.
- Zie [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)voor een PowerShell-cmdletreferentie.
- Zie Azure Automation State Configuration Pricing voor prijsinformatie voor [prijsinformatie.](https://azure.microsoft.com/pricing/details/automation/)
- Zie Continue implementatie naar virtuele machines met Azure [Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md)voor een voorbeeld van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.
