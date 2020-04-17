---
title: Onboarding-machines voor beheer door Azure Automation State Configuration
description: Machines instellen voor beheer met Azure Automation State Configuration
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457736"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Onboarding-machines voor beheer door Azure Automation State Configuration

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Waarom machines beheren met Azure Automation State Configuration?

Azure Automation State Configuration is a configuration management service for Desired State Configuration (DSC) nodes in any cloud or on-premises datacenter. Het is toegankelijk in de Azure-portal door **statusconfiguratie (DSC)** te selecteren onder **Configuratiebeheer**. 

Deze service maakt schaalbaarheid over duizenden machines snel en eenvoudig mogelijk vanaf een centrale, veilige locatie. U eenvoudig machines aan boord nemen, ze declaratieve configuraties toewijzen en rapporten weergeven die aangeven dat elke machine voldoet aan de gewenste status die u opgeeft.

De azure automation state configuration service is aan DSC wat Azure Automation runbooks zijn voor PowerShell-scripting. Met andere woorden, op dezelfde manier dat Azure Automation u helpt bij het beheren van PowerShell-scripts, helpt het u ook bij het beheren van DSC-configuraties. Zie Azure [Automation State Configuration Overview](automation-dsc-overview.md)voor meer informatie over de voordelen van het gebruik van Azure Automation State Configuration.

Azure Automation State Configuration kan worden gebruikt om verschillende machines te beheren:

- Virtuele machines van Azure
- Azure virtuele machines (klassiek)
- Fysieke/virtuele Windows-machines on-premises of in een andere cloud dan Azure (inclusief AWS EC2-exemplaren)
- Fysieke/virtuele Linux-machines on-premises, in Azure of in een andere cloud dan Azure

Als u nog niet klaar bent om de machineconfiguratie vanuit de cloud te beheren, u Azure Automation State Configuration gebruiken als een eindpunt voor alleen rapport. Met deze functie u (push)-configuraties instellen via DSC en rapportagedetails weergeven in Azure Automation.

> [!NOTE]
> Azure VM's beheren met Azure Automation State Configuration is gratis inbegrepen als de geïnstalleerde Azure VM Desired State Configuration extension version groter is dan 2.70. Zie [**Prijspagina Automatisering voor**](https://azure.microsoft.com/pricing/details/automation/)meer informatie.

In de volgende secties van dit artikel wordt beschreven hoe u de hierboven vermelde machines aanpassen aan azure automation state configuration.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="onboarding-azure-vms"></a>Onboarding Azure VM's

Met Azure Automation State Configuration u eenvoudig azure VM's instappen voor configuratiebeheer, met behulp van de Azure-portal, Azure Resource Manager-sjablonen of PowerShell. Onder de motorkap en zonder dat een beheerder een afstandsbediening in een VM hoeft te gebruiken, registreert de azure VM Desired State Configuration extension de VM met Azure Automation State Configuration. Aangezien de Azure-extensie asynchroon wordt uitgevoerd, worden stappen om de voortgang bij te houden of problemen op te lossen aangeboden in het gedeelte [Azure-onboarding](#troubleshooting-azure-virtual-machine-onboarding) voor virtuele machines van dit artikel.

> [!NOTE]
>Het implementeren van DSC naar een Linux-knooppunt maakt gebruik van de **/tmp-map.** Modules zoals `nxautomation` worden tijdelijk gedownload voor verificatie voordat ze op de juiste locaties worden geïnstalleerd. Om ervoor te zorgen dat modules correct worden geïnstalleerd, heeft de Log Analytics-agent voor Linux lees-/schrijfmachtigingen nodig voor de **/tmp-map.**<br><br>
>De Log Analytics-agent voor `omsagent` Linux wordt uitgevoerd als gebruiker. Als u de `omsagent` gebruiker >schrijftoestemming `setfacl -m u:omsagent:rwx /tmp`wilt verlenen, voert u de opdracht uit .

### <a name="onboard-a-vm-using-azure-portal"></a>Aan boord van een VM met Azure-portal

Ga als lid aan boord van een Azure VM naar Azure Automation State Configuration via de [Azure-portal:](https://portal.azure.com/)

1. Navigeer naar het Azure Automation-account waarin vm's aan boord gaan. 

2. Selecteer op de pagina Statusconfiguratie het tabblad **Knooppunten** en klik op **Toevoegen**.

3. Kies een VM aan boord.

4. Als de machine niet de gewenste powershell-extensie heeft geïnstalleerd en de energiestatus wordt uitgevoerd, klikt u op **Verbinden**.

5. Voer **onder Registratie**de [PowerShell DSC Local Configuration Manager-waarden](/powershell/scripting/dsc/managing-nodes/metaConfig) in die vereist zijn voor uw use case. Optioneel u een knooppuntconfiguratie invoeren die u aan de VM wilt toewijzen.

![onboarding](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Een VM aan boord met Azure Resource Manager-sjablonen

U een VM implementeren en aan boord nemen van Azure Automation State Configuration met Azure Resource Manager-sjablonen. Zie [Server beheerd door de betekening of seivan configuratie](https://azure.microsoft.com/resources/templates/101-automation-configuration/) door de gewenste status voor een voorbeeldsjabloon die een bestaande VM aandebeidert in Azure Automation State Configuration. Als u een virtuele machineschaalset beheert, raadpleegt u de voorbeeldsjabloon in [de configuratie van de virtuele machineschaalset die wordt beheerd door Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-machines-using-powershell"></a>Boordmachines met PowerShell

U de [cmdlet Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) in PowerShell gebruiken om VM's aan boord te nemen van Azure Automation State Configuration. 

> [!NOTE]
>De `Register-AzAutomationDscNode` cmdlet is momenteel alleen geïmplementeerd voor machines met Windows, omdat het alleen de Windows-extensie activeert.

### <a name="register-vms-across-azure-subscriptions"></a>VM's registreren voor Azure-abonnementen

De beste manier om VM's van andere Azure-abonnementen te registreren, is door de DSC-extensie te gebruiken in een Azure Resource Manager-implementatiesjabloon. Voorbeelden worden gegeven in [de extensie Gewenste statusconfiguratie met Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Zie de [onboarding veilig met behulp van de registratiesectie](#onboarding-securely-using-registration) in dit artikel om de registratiesleutel en registratie-URL te vinden die u als parameters in de sjabloon wilt gebruiken.

## <a name="onboarding-physicalvirtual-windows-machines"></a>Onboarding fysieke/virtuele Windows-machines

U Windows-servers die on-premises of in andere cloudomgevingen (inclusief AWS EC2-exemplaren) worden uitgevoerd, inbouwen op Azure Automation State Configuration. De servers moeten [uitgaande toegang hebben tot Azure.](automation-dsc-overview.md#network-planning)

1. Zorg ervoor dat de nieuwste versie van [WMF 5](https://aka.ms/wmf5latest) is geïnstalleerd op de machines om aan boord te gaan van Azure Automation State Configuration. Bovendien moet WMF 5 worden geïnstalleerd op de computer die u gebruikt voor de onboarding-bewerking.
1. Volg de aanwijzingen in de sectie [DSC-metaconfiguraties genereren](#generating-dsc-metaconfigurations) om een map te maken met de vereiste DSC-metaconfiguraties. 
1. Gebruik de volgende cmdlet om de PowerShell DSC-metaconfiguraties op afstand toe te passen op de machines die u aan boord wilt hebben. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Als u de PowerShell DSC-metaconfiguraties niet op afstand toepassen, kopieert u de map **metaconfiguraties** naar de machines die u insuurt. Voeg vervolgens code toe om [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) lokaal op de machines aan te roepen.
1. Controleer met de Azure-portal of cmdlets of de machines aan boord worden weergegeven als statusconfiguratieknooppunten die zijn geregistreerd in uw Azure Automation-account.

## <a name="onboarding-physicalvirtual-linux-machines"></a>Onboarding fysieke/virtuele Linux machines

U Linux-servers die on-premises of in andere cloudomgevingen worden uitgevoerd, inbouwen op Azure Automation State Configuration. De servers moeten [uitgaande toegang hebben tot Azure.](automation-dsc-overview.md#network-planning)

1. Zorg ervoor dat de nieuwste versie van [powershell gewenste statusconfiguratie voor Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) is geïnstalleerd op de machines om aan boord te gaan van Azure Automation State Configuration.
2. Als de [PowerShell DSC Local Configuration Manager standaard](/powershell/scripting/dsc/managing-nodes/metaConfig4) overeenkomt met uw use case en u machines aan boord wilt nemen, zodat ze zowel uit de configuratie van Azure Automation State trekken als rapporteren:

   - Gebruik op elke Linux-machine aan boord `Register.py` van Azure Automation State Configuration de standaardinstellingen voor PowerShell DSC Local Configuration Manager.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Zie de onboarding veilig met behulp van [het registratiegedeelte](#onboarding-securely-using-registration) van dit artikel om de registratiesleutel en registratie-URL voor uw Automatiseringsaccount te vinden.

3. Als de standaardinstellingen van PowerShell DSC Local Configuration Manager (LcM) niet overeenkomen met uw use case of als u machines aan boord wilt die alleen rapporteren aan Azure Automation State Configuration, voert u stappen 4-7 uit. Ga anders rechtstreeks naar stap 7.

4. Volg de aanwijzingen in de sectie [DSC-metaconfiguraties genereren](#generating-dsc-metaconfigurations) om een map te produceren met de vereiste DSC-metaconfiguraties.

5. Zorg ervoor dat de nieuwste versie van [WMF 5](https://aka.ms/wmf5latest) is geïnstalleerd op de machine die wordt gebruikt voor onboarding.

6. Voeg de code als volgt toe om de PowerShell DSC-metaconfiguraties op afstand toe te passen op de machines die u aan boord wilt hebben.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Als u de PowerShell DSC-metaconfiguraties niet op afstand toepassen, kopieert u de metaconfiguraties die overeenkomen met de externe machines uit de map die in stap 4 is beschreven, naar de Linux-machines.

8. Voeg code `Set-DscLocalConfigurationManager.py` toe om lokaal op elke Linux-machine aan te bellen om aan boord te gaan van Azure Automation State Configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Zorg ervoor dat de machines aan boord nu worden weergegeven als DSC-knooppunten die zijn geregistreerd in uw Azure Automation-account.

## <a name="generating-dsc-metaconfigurations"></a>DSC-metaconfiguraties genereren

Als u een machine wilt aan boord van Azure Automation State Configuration, u een [DSC-metaconfiguratie](/powershell/scripting/dsc/managing-nodes/metaConfig)genereren. Met deze configuratie moet de DSC-agent uit azure automation state configuration trekken en/of rapporteren. U een DSC-metaconfiguratie voor Azure Automation State Configuration genereren met behulp van een PowerShell DSC-configuratie of de Azure Automation PowerShell-cmdlets.

> [!NOTE]
> DSC-metaconfiguraties bevatten de geheimen die nodig zijn om een machine aan boord te nemen van een automatiseringsaccount voor beheer. Zorg ervoor dat u alle DSC-metaconfiguraties die u maakt, goed beschermt of verwijder ze na gebruik.

Proxy-ondersteuning voor metaconfiguraties wordt beheerd door LCM, de Windows PowerShell DSC-engine. De LCM draait op alle doelknooppunten en is verantwoordelijk voor het aanroepen van de configuratiebronnen die zijn opgenomen in een DSC-metaconfiguratiescript. U proxy-ondersteuning opnemen in een metaconfiguratie door definities van de `ConfigurationRepositoryWeb` `ResourceRepositoryWeb`proxy-URL en de proxy-referentie op te nemen indien nodig in de, en `ReportServerWeb` blokken. Zie [Lokaal configuratiebeheer configureren](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>DSC-metaconfiguraties genereren met behulp van een DSC-configuratie

1. Open VSCode (of uw favoriete editor) als beheerder op een machine in uw lokale omgeving. De machine moet de nieuwste versie van [WMF 5](https://aka.ms/wmf5latest) hebben geïnstalleerd.
1. Kopieer het volgende script lokaal. Dit script bevat een PowerShell DSC-configuratie voor het maken van metaconfiguraties en een opdracht om de metaconfiguratie-creatie af te trappen.

    > [!NOTE]
    > Namen configuratieknooppunt configuratieknooppunt zijn hoofdlettergevoelig in de Azure-portal. Als de aanvraag niet is afgestemd, wordt het knooppunt niet weergegeven onder het tabblad **Knooppunten.**

   ```powershell
   # The DSC configuration that will generate metaconfigurations
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

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
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

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Vul de registratiesleutel en URL in voor uw Automatiseringsaccount, evenals de namen van de machines aan boord. Alle andere parameters zijn optioneel. Zie de [onboarding veilig via de registratiesectie](#onboarding-securely-using-registration) om de registratiesleutel en registratie-URL voor uw Automatiseringsaccount te vinden.

1. Als u wilt dat de machines DSC-statusgegevens rapporteren aan azure automation state `ReportOnly` configuration, maar niet de configuratie of PowerShell-modules ophalen, stelt u de parameter in op true.

1. Als `ReportOnly` deze niet is ingesteld, rapporteren de machines DSC-statusgegevens aan Azure Automation State Configuration and pull configuration or PowerShell modules. Stel parameters dienovereenkomstig in de `ConfigurationRepositoryWeb`, `ResourceRepositoryWeb`, en `ReportServerWeb` blokken.

1. Voer het script uit. U moet nu een werkende map map genaamd **DscMetaConfigs**, met de PowerShell DSC metaconfiguraties voor de machines aan boord (als beheerder).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>DSC-metaconfiguraties genereren met Azure Automation-cmdlets

Als PowerShell DSC LCM standaardinstellingen overeenkomen met uw use case en u machines aan boord wilt nemen om zowel uit azure automation state configuration te trekken als te rapporteren, u de benodigde DSC-metaconfiguraties eenvoudiger genereren met behulp van de Azure Automation-cmdlets.

1. Open de PowerShell-console of VSCode als beheerder op een machine in uw lokale omgeving.
2. Maak verbinding met Azure Resource Manager met [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Download de PowerShell DSC-metaconfiguraties voor de machines die u aan boord wilt nemen van het Automation-account waarin u knooppunten instelt.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. U moet nu een **DscMetaConfigs-map** hebben met de PowerShell DSC-metaconfiguraties voor de machines aan boord (als beheerder).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Veilig onboarden met behulp van registratie

Machines kunnen veilig aan boord gaan van een Azure Automation-account via het WMF 5 DSC-registratieprotocol. Met dit protocol kan een DSC-knooppunt worden geverifieerd naar een PowerShell DSC-pull- of rapportserver, inclusief Azure Automation State Configuration. Het knooppunt registreert zich bij de server bij de registratie-URL en verifieert met behulp van een registratiesleutel. Tijdens de registratie onderhandelen de DSC-knooppunt en de DSC-pull/report-server over een uniek certificaat voor het knooppunt dat moet worden gebruikt voor verificatie naar de server na registratie. Dit proces voorkomt dat aan boord zittende knooppunten zich voordoen als elkaar, bijvoorbeeld als een knooppunt wordt aangetast en zich kwaadwillig gedraagt. Na registratie wordt de registratiesleutel niet opnieuw gebruikt voor verificatie en wordt deze verwijderd uit het knooppunt.

U de vereiste informatie voor het configuratieregistratieprotocol voor statusophalen ophalen bij **Sleutels** onder **Accountinstellingen** in de Azure-portal. 

![Azure-automatiseringssleutels en -URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Registratie-URL is het URL-veld op de pagina Sleutels.
- Registratiesleutel is de waarde van het veld **Primaire toegangssleutel** of het veld **Secundaire toegangssleutel** op de pagina Sleutels. Beide sleutels kunnen worden gebruikt.

Voor extra beveiliging u de primaire en secundaire toegangssleutels van een Automatiseringsaccount op elk gewenst moment regenereren op de pagina Sleutels. Sleutelregeneratie voorkomt dat toekomstige knooppuntregistraties eerdere sleutels gebruiken.

## <a name="re-registering-a-node"></a>Een knooppunt opnieuw registreren

Nadat u een machine hebt geregistreerd als Een DSC-knooppunt in azure-automatiseringsstatusconfiguratie, zijn er verschillende redenen waarom u dat knooppunt in de toekomst opnieuw moet registreren.

- **Certificaatverlenging.** Voor versies van Windows Server vóór Windows Server 2019 onderhandelt elk knooppunt automatisch over een uniek certificaat voor verificatie dat na een jaar verloopt. Als een certificaat verloopt zonder verlenging, kan het knooppunt niet `Unresponsive`communiceren met Azure Automation en is het gemarkeerd . Momenteel kan het PowerShell DSC-registratieprotocol certificaten niet automatisch verlengen wanneer ze bijna verlopen en moet u de knooppunten na een jaar opnieuw registreren. Voordat u zich opnieuw registreert, moet u ervoor zorgen dat op elk knooppunt WMF 5 RTM wordt uitgevoerd. 

    Herregistratie die 90 dagen of minder wordt uitgevoerd vanaf de vervaldatum van het certificaat, of op enig moment na de vervaldatum van het certificaat, resulteert in een nieuw certificaat dat wordt gegenereerd en gebruikt. Een oplossing voor dit probleem is opgenomen in Windows Server 2019 en hoger.

- **Wijzigingen in DSC LCM-waarden.** Mogelijk moet [u de PowerShell DSC LCM-waarden](/powershell/scripting/dsc/managing-nodes/metaConfig4) wijzigen die zijn `ConfigurationMode`ingesteld tijdens de eerste registratie van het knooppunt, bijvoorbeeld . Momenteel u deze DSC-agentwaarden alleen wijzigen door opnieuw te registreren. De enige uitzondering is de waarde knooppuntconfiguratie die aan het knooppunt is toegewezen. U dit rechtstreeks wijzigen in Azure Automation DSC.

U een knooppunt opnieuw registreren, net zoals u het knooppunt in eerste instantie hebt geregistreerd, met behulp van een van de onboarding-methoden die in dit document worden beschreven. U hoeft het registreren van een knooppunt niet uit te schrijven bij Azure Automation State Configuration voordat u het opnieuw registreert.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Problemen met Azure virtual machine onboarding oplossen

Azure Automation State Configuration stelt u eenvoudig in te sluiten op Azure Windows VM's voor configuratiebeheer. Onder de motorkap wordt de Azure VM Desired State Configuration extension gebruikt om de VM te registreren met Azure Automation State Configuration. Aangezien de azure VM Desired State Configuration-extensie asynchroon wordt uitgevoerd, kan het belangrijk zijn de voortgang ervan bij te houden en problemen op te lossen.

> [!NOTE]
> Het kan tot een uur duren voordat Azure Automation deze als geregistreerd heeft om een Azure Windows VM in te zetten voor azure automation in azure automation. Deze vertraging is te wijten aan de installatie van WMF 5 op de VM door de Azure VM Desired State Configuration extension, die nodig is om de VM aan boord te gaan van Azure Automation State Configuration.

Ga als het gaat om het oplossen of weergeven van de status van de configuratie-extensie gewenste azure vm:

1. Navigeer in de Azure-portal naar de VM die wordt aanboord.
2. Klik op **Extensies** onder **Instellingen**. 
3. Selecteer nu **DSC** of **DSCForLinux,** afhankelijk van uw besturingssysteem. 
4. Voor meer informatie u op **Gedetailleerde status weergeven**klikken.

Zie [Problemen met Azure Automation Desired State Configuration (DSC) voor](./troubleshoot/desired-state-configuration.md)meer informatie over probleemoplossing.

## <a name="next-steps"></a>Volgende stappen

- Zie [Aan de slag met Azure Automation State Configuration](automation-dsc-getting-started.md)om aan de slag te gaan.
- Zie [Configuraties compileren in Azure Automation State Configuration](automation-dsc-compile.md)voor meer informatie over het samenstellen van DSC-configuraties, zodat u ze toewijzen aan doelknooppunten.
- Zie [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)voor een PowerShell-cmdletreferentie.
- Zie Azure Automation State Configuration Pricing voor prijsinformatie voor [prijsinformatie.](https://azure.microsoft.com/pricing/details/automation/)
- Zie [Gebruiksvoorbeeld: Continue implementatie naar virtuele machines Met Azure Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md)voor een voorbeeld van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.
