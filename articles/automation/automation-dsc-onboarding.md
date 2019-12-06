---
title: Onboarding van machines voor beheer door Azure Automation status configuratie
description: Computers instellen voor beheer met Azure Automation status configuratie
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: 80038cf5fba18eca4fbbe1405df2a76cfc84e2db
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850326"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Onboarding van machines voor beheer door Azure Automation status configuratie

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Waarom computers met Azure Automation status configuratie beheren?

Azure Automation status configuratie is een configuratie beheer service voor DSC-knoop punten in elke Cloud of een on-premises Data Center.
Hiermee kan de schaal baarheid van duizenden computers snel en eenvoudig worden uitgebreid vanaf een centrale, veilige locatie.
U kunt eenvoudig computers onboarden, de declaratieve configuraties toewijzen en rapporten weer geven met de naleving van elke computer die u hebt opgegeven.
De Azure Automation State Configuration-service is van DSC wat Azure Automation runbooks zijn voor het uitvoeren van Power shell-scripts.
Met andere woorden, op dezelfde manier als Azure Automation u bij het beheren van Power shell-scripts, kunt u ook DSC-configuraties beheren.
Zie [Azure Automation status configuratie Overview](automation-dsc-overview.md)(Engelstalig) voor meer informatie over de voor delen van het gebruik van Azure Automation status configuratie.

Azure Automation status configuratie kan worden gebruikt voor het beheren van verschillende computers:

- Virtuele machines van Azure
- Azure virtual machines (klassiek)
- Amazon Web Services (AWS) EC2-instanties
- Fysieke/virtuele Windows-machines on-premises of in een andere Cloud dan Azure/AWS
- Fysieke/virtuele Linux-machines on-premises, in azure of in een andere Cloud dan Azure

Als u de computer configuratie niet kunt beheren vanuit de Cloud, kan de configuratie van Azure Automation-status ook worden gebruikt als een eind punt voor alleen een rapport.
Hiermee kunt u configuratie (push) configuraties via DSC instellen en rapportage details weer geven in Azure Automation.

> [!NOTE]
> Het beheren van Azure-Vm's met status configuratie is zonder extra kosten inbegrepen als de DSC-extensie van de virtuele machine is geïnstalleerd, groter is dan 2,70. Raadpleeg de [**pagina met prijzen voor Automation**](https://azure.microsoft.com/pricing/details/automation/) voor meer informatie.

In de volgende secties wordt beschreven hoe u elk type machine kunt toevoegen aan Azure Automation status configuratie.

## <a name="azure-virtual-machines"></a>Virtuele machines van Azure

Met de configuratie van de Azure Automation-status kunt u Azure virtual machines eenvoudig voor configuratie beheer uitvoeren met behulp van de Azure Portal, Azure Resource Manager sjablonen of Power shell. Op de schermen en zonder een beheerder die op afstand in de virtuele machine moet worden uitgevoerd, registreert de Azure VM desired state Configuration extension de VM met Azure Automation status configuratie.
Omdat de configuratie-uitbrei ding desired state van Azure VM asynchroon wordt uitgevoerd, stappen om de voortgang op te sporen of problemen op te lossen, vindt u in de volgende sectie [**problemen met het voorbereiden van een virtuele machine in azure oplossen**](#troubleshooting-azure-virtual-machine-onboarding) .

### <a name="azure-portal"></a>Azure Portal

Ga in het [Azure Portal](https://portal.azure.com/)naar het Azure Automation account waar u virtuele machines wilt voorbereiden. Klik op de pagina status configuratie en het tabblad **knoop punten** op **+ toevoegen**.

Selecteer een virtuele Azure-machine die u wilt vrijgeven.

Als op de computer niet de gewenste uitbrei ding voor Power shell is geïnstalleerd en de energie status actief is, klikt u op **verbinding maken**.

Voer onder **registratie**de [lokale Power shell DSC-Configuration Manager waarden](/powershell/scripting/dsc/managing-nodes/metaConfig) in die vereist zijn voor uw use-case en eventueel een knooppunt configuratie die aan de virtuele machine moet worden toegewezen.

![onboarding](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

Virtuele Azure-machines kunnen worden geïmplementeerd en gedistribueerd naar Azure Automation status configuratie via Azure Resource Manager sjablonen. Zie [server beheerd door desired state Configuration-service](https://azure.microsoft.com/resources/templates/101-automation-configuration/) voor een voorbeeld sjabloon die een bestaande virtuele machine uitschakelt om de status configuratie te Azure Automation.
Als u een Schaalset voor virtuele machines beheert, raadpleegt u de voorbeeld sjabloon [configuratie van VM-schaal sets die wordt beheerd door Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

De cmdlet [REGI ster-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) kan worden gebruikt om virtuele machines in azure op te heffen met behulp van Power shell.
Dit wordt momenteel echter alleen geïmplementeerd voor computers met Windows (de cmdlet activeert alleen de Windows-extensie).

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Virtuele machines in azure-abonnementen registreren

De beste manier om virtuele machines van andere Azure-abonnementen te registreren, is door gebruik te maken van de DSC-extensie in een Azure Resource Manager-implementatie sjabloon.
Voor beelden vindt u in de [desired state Configuration extension met Azure Resource Manager sjablonen](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
Als u de registratie sleutel en registratie-URL wilt vinden die u wilt gebruiken als para meters in de sjabloon, raadpleegt u de sectie de volgende [**veilige registratie**](#secure-registration) .

## <a name="amazon-web-services-aws-virtual-machines"></a>Amazon Web Services (AWS) virtuele machines

Met de AWS DSC Toolkit kunt u eenvoudig Amazon Web Services virtuele machines voorbereiden voor configuratie beheer door Azure Automation status configuratie. Meer informatie over de Toolkit vindt u [hier](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Fysieke/virtuele Windows-machines on-premises of in een andere Cloud dan Azure/AWS

Windows-servers die on-premises of in andere Cloud omgevingen worden uitgevoerd, kunnen ook worden gewaakd naar Azure Automation status configuratie, zolang ze [uitgaande toegang hebben tot Azure](automation-dsc-overview.md#network-planning):

1. Zorg ervoor dat de meest recente versie van [WMF 5](https://aka.ms/wmf5latest) is geïnstalleerd op de computers die u wilt voorbereiden op Azure Automation status configuratie.
1. Volg de instructies in de volgende sectie [**DSC-mailconfiguraties**](#generating-dsc-metaconfigurations) genereren voor het genereren van een map met de benodigde DSC-configuratie.
1. Pas de Power shell DSC-configuratie op afstand toe op de computers die u wilt voorbereiden. **Op de computer waarop deze opdracht wordt uitgevoerd, moet de meest recente versie van [WMF 5](https://aka.ms/wmf5latest) zijn geïnstalleerd**:

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Als u de Power shell DSC-webconfiguraties niet op afstand kunt Toep assen, kopieert u de map met de-configuratie van stap 2 op elke computer naar onboarding. Roep vervolgens **set-DscLocalConfigurationManager** lokaal op elke computer aan voor onboarding.
1. Controleer met behulp van de Azure Portal of cmdlets of de computers die u wilt voorbereiden, nu worden weer gegeven als status configuratie knooppunten die zijn geregistreerd in uw Azure Automation-account.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Fysieke/virtuele Linux-machines on-premises of in een andere Cloud dan Azure

Linux-servers die on-premises of in andere Cloud omgevingen worden uitgevoerd, kunnen ook worden gewaakd naar Azure Automation status configuratie, zolang ze [uitgaande toegang hebben tot Azure](automation-dsc-overview.md#network-planning):

1. Zorg ervoor dat de meest recente versie van de [Power shell desired state Configuration voor Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) is geïnstalleerd op de computers die u wilt voorbereiden op Azure Automation status configuratie.
1. Als de [lokale Power shell DSC-Configuration Manager standaard waarden](/powershell/scripting/dsc/managing-nodes/metaConfig4) overeenkomen met uw use-case, en u computers wilt voorbereiden, zodat deze **beide** worden opgehaald en rapporteren aan Azure Automation status configuratie:

   - Gebruik op elke Linux-computer om de configuratie van de Azure Automation-status onboarding uit te kunnen `Register.py` de standaard instellingen van Power shell DSC Local Configuration Manager:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Als u de registratie sleutel en registratie-URL voor uw Automation-account wilt vinden, raadpleegt u de sectie de volgende [**beveiligde registratie**](#secure-registration) .

     Als de lokale Power shell DSC-Configuration Manager standaard waarden **niet** overeenkomen met uw use-case of als u computers wilt voorbereiden die alleen rapporteren aan Azure Automation status configuratie, volgt u stap 3-6. Ga anders verder met stap 6.

1. Volg de instructies in de volgende sectie [**DSC-mailconfiguraties genereren**](#generating-dsc-metaconfigurations) om een map te genereren met de benodigde DSC-configuratie.
1. De Power shell DSC-configuratie op afstand Toep assen op de computers die u wilt vrijgeven:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Op de computer waarop deze opdracht wordt uitgevoerd, moet de meest recente versie van [WMF 5](https://aka.ms/wmf5latest) zijn geïnstalleerd.

1. Als u de Power shell DSC-webconfiguraties niet op afstand kunt Toep assen, kopieert u de door de map in stap 5 op de Linux-computer overeenkomende configuratie van de betreffende computer. Roep vervolgens `SetDscLocalConfigurationManager.py` lokaal aan op elke Linux-computer die u wilt voorbereiden op Azure Automation status configuratie:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Als u de Azure Portal of cmdlets wilt gebruiken, controleert u of de computers voor de onboarding nu worden weer gegeven als DSC-knoop punten die zijn geregistreerd in uw Azure Automation-account.

## <a name="generating-dsc-metaconfigurations"></a>DSC-configuratie genereren

Voor een algemene onboarding van elke machine naar Azure Automation status configuratie kan een [DSC-configuratie](/powershell/scripting/dsc/managing-nodes/metaConfig) worden gegenereerd die aangeeft dat de DSC-agent moet worden opgehaald van en/of rapport naar Azure Automation status configuratie. DSC-configuratie voor de configuratie van de Azure Automation-status kan worden gegenereerd met behulp van een Power shell DSC-configuratie of met de Azure Automation Power shell-cmdlets.

> [!NOTE]
> DSC-mailconfiguraties bevatten de geheimen die nodig zijn om een machine vrij te kunnen uitvoeren naar een Automation-account voor beheer. Zorg ervoor dat u de DSC-configuratie die u maakt op de juiste wijze beveiligt, of verwijder ze na gebruik.

### <a name="using-a-dsc-configuration"></a>Een DSC-configuratie gebruiken

1. Open de VSCode (of uw favoriete editor) als beheerder op een computer in uw lokale omgeving. Op de computer moet de nieuwste versie van [WMF 5](https://aka.ms/wmf5latest) zijn geïnstalleerd.
1. Kopieer het volgende script lokaal. Dit script bevat een Power shell DSC-configuratie voor het maken van een eigen configuratie en een opdracht voor het maken van de configuratie van de-de-de-de-de-de

> [!NOTE]
> Naam configuratie knooppunt configuratie namen zijn hoofdletter gevoelig in de portal. Als de aanvraag niet overeenkomt met het knoop punt, wordt niet weer gegeven op het tabblad **knoop punten** .

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

1. Vul de registratie sleutel en de URL voor uw Automation-account in, evenals de namen van de computers die u wilt voorbereiden. Alle andere para meters zijn optioneel. Als u de registratie sleutel en registratie-URL voor uw Automation-account wilt vinden, raadpleegt u de sectie de volgende [**beveiligde registratie**](#secure-registration) .
1. Als u wilt dat de computers DSC-status informatie rapporteren aan Azure Automation status configuratie, maar geen configuratie-of Power shell-modules, stelt u de para meter **ReportOnly** in op True.
1. Voer het script uit. U hebt nu een map met de naam **DscMetaConfigs** in uw werkmap, met daarin de Power shell DSC-bankconfiguraties voor de machines voor onboarding (als beheerder):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>De cmdlets van Azure Automation gebruiken

Als de lokale Configuration Manager standaard instellingen van Power shell overeenkomen met uw use-case en u de computers wilt opdoen, zodat ze worden opgehaald van en rapporteren aan Azure Automation status configuratie, bieden de Azure Automation-cmdlets een vereenvoudigde methode voor het genereren van de DSC-configuratie die nodig is:

1. Open de Power shell-console of VSCode als beheerder op een computer in uw lokale omgeving.
2. Verbinding maken met Azure Resource Manager met behulp van `Connect-AzAccount`
3. Down load de Power shell DSC-configuratie voor de computers die u wilt vrijgeven uit het Automation-account waarvoor u de knoop punten wilt voorbereiden:

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. U hebt nu een map met de naam ***DscMetaConfigs***, met daarin de Power shell DSC-configuratie voor de machines voor onboarding (als beheerder):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Veilige registratie

Machines kunnen veilig onboarden op een Azure Automation-account via het WMF 5 DSC-registratie protocol, waarmee een DSC-knoop punt kan worden geverifieerd bij een Power shell DSC-pull-of-rapport server (inclusief Azure Automation status configuratie). Het knoop punt registreert de server op een **registratie-URL**en verifieert met behulp van een **registratie sleutel**. Tijdens de registratie onderhandelt het DSC-knoop punt en de DSC pull/Reporting-Server een uniek certificaat voor dit knoop punt om te gebruiken voor verificatie bij de server post-registratie. Dit proces voor komt dat niet-gehoste knoop punten elkaar nabootsen, bijvoorbeeld wanneer een knoop punt is aangetast en zich op een kwaad aardig manier bevindt. Na de registratie wordt de registratie sleutel niet opnieuw gebruikt voor verificatie en wordt deze verwijderd uit het knoop punt.

U kunt de informatie ophalen die is vereist voor het status configuratie registratie protocol uit **sleutels** onder **account instellingen** in de Azure Portal. Open deze Blade door te klikken op het sleutel pictogram in het deel venster **Essentials** voor het Automation-account.

![Sleutels en URL van Azure Automation](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Registratie-URL is het URL-veld op de Blade sleutels beheren.
- De registratie sleutel is de primaire toegangs sleutel of secundaire toegangs sleutel op de Blade sleutels beheren. Beide sleutels kunnen worden gebruikt.

Voor extra beveiliging kunnen de primaire en secundaire toegangs sleutels van een Automation-account op elk gewenst moment opnieuw worden gegenereerd (op de pagina **sleutels beheren** ) om toekomstige knooppunt registraties te voor komen met eerdere sleutels.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Problemen met het voorbereiden van virtuele Azure-machines oplossen

Met de configuratie van de Azure Automation-status kunt u op eenvoudige wijze Azure Windows-Vm's vrijgeven voor configuratie beheer. Onder de schermen wordt de Azure VM desired state Configuration-extensie gebruikt voor het registreren van de virtuele machine met Azure Automation status configuratie. Omdat de configuratie-uitbrei ding desired state van Azure VM asynchroon wordt uitgevoerd, kunt u de voortgang bijhouden en de uitvoering ervan oplossen.

> [!NOTE]
> Een methode voor het onboarden van een Azure Windows-VM naar Azure Automation status configuratie die gebruikmaakt van de desired state Configuration-extensie voor Azure VM, kan tot een uur duren voordat het knoop punt wordt weer gegeven als geregistreerd in Azure Automation. Dit wordt veroorzaakt door de installatie van Windows Management Framework 5,0 op de virtuele machine door de Azure VM DSC-extensie, die vereist is om de virtuele machine vrij te Azure Automation status configuratie.

Om problemen op te lossen of de status weer te geven van de extensie voor de gewenste status van de Azure VM, in de Azure Portal navigeert u naar de virtuele machine die wordt uitgevoerd en klikt u op **uitbrei dingen** onder **instellingen**. Klik op **DSC** of **DSCForLinux** , afhankelijk van uw besturings systeem. Klik op **gedetailleerde status weer geven**voor meer informatie.

## <a name="certificate-expiration-and-reregistration"></a>Certificaten verlopen en opnieuw registreren

Na het registreren van een computer als een DSC-knoop punt in Azure Automation status configuratie, zijn er een aantal redenen waarom u dat knoop punt in de toekomst mogelijk opnieuw moet registreren:

- Voor versies van Windows Server voorafgaand aan Windows Server 2019 onderhandelt elk knoop punt automatisch een uniek certificaat voor verificatie dat verloopt na één jaar. Op dit moment kan het Power shell DSC-registratie protocol niet automatisch certificaten vernieuwen wanneer deze bijna verlopen. u moet de knoop punten na een jaar opnieuw registreren. Zorg ervoor dat op elk knoop punt Windows Management Framework 5,0 RTM wordt uitgevoerd voordat u zich opnieuw registreert. Als het verificatie certificaat van een knoop punt verloopt en het knoop punt niet opnieuw wordt geregistreerd, kan het knoop punt niet communiceren met Azure Automation en is de markering niet meer reageert. Opnieuw registreren heeft 90 dagen of minder tijd uitgevoerd vanaf de verval datum van het certificaat of op elk moment na de verval tijd van het certificaat, waardoor er een nieuw certificaat wordt gegenereerd en gebruikt.  Een oplossing voor dit probleem is opgenomen in Windows Server 2019 en hoger.
- Het wijzigen van [lokale Power shell DSC-Configuration Manager waarden](/powershell/scripting/dsc/managing-nodes/metaConfig4) die zijn ingesteld tijdens de eerste registratie van het knoop punt, zoals ConfigurationMode. Deze DSC-agent waarden kunnen momenteel alleen worden gewijzigd via opnieuw registreren. De enige uitzonde ring is de knooppunt configuratie die is toegewezen aan het knoop punt. Dit kan rechtstreeks in Azure Automation DSC worden gewijzigd.

Opnieuw registreren kan op dezelfde manier worden uitgevoerd als u het knoop punt in eerste instantie hebt geregistreerd, met behulp van een van de voorbereidings methoden die in dit document worden beschreven. U hoeft de registratie van een knoop punt bij Azure Automation status configuratie niet ongedaan te maken voordat u het opnieuw registreert.

## <a name="next-steps"></a>Volgende stappen

- Zie aan de slag [met de configuratie van de Azure Automation-status](automation-dsc-getting-started.md) om aan de slag te gaan.
- Zie [configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md) voor meer informatie over het compileren van DSC-configuraties zodat u ze aan doel knooppunten kunt toewijzen.
- Zie [Azure Automation status configuratie-cmdlets](/powershell/module/az.automation#automation) voor informatie over de Power shell-cmdlet.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/) voor prijs informatie.
- Voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie gaat u naar [continue implementatie met behulp van Azure Automation-status configuratie en chocolade](automation-dsc-cd-chocolatey.md)
