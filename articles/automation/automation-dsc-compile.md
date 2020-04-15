---
title: DSC-configuraties compileren in Azure-automatiseringsstatusconfiguratie
description: In dit artikel wordt beschreven hoe u DSC-configuraties (Desired State Configuration) compileren voor Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 3145c7db064432e443aae5dcd503905b865ffe46
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383253"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>DSC-configuraties compileren in Azure-automatiseringsstatusconfiguratie

U de configuraties van de gewenste statusconfiguratie (DSC) op de volgende manieren compileren in azure-automatiseringsstatusconfiguratie:

- Compilatieservice azure state configuration
  - Beginnersmethode met interactieve gebruikersinterface
   - De status van de taak eenvoudig bijhouden

- Windows PowerShell
  - Bellen vanuit Windows PowerShell op lokaal werkstation of buildservice
  - Integreren met ontwikkelingstestpijplijn
  - Complexe parameterwaarden opgeven
  - Werken met knooppuntgegevens en niet-knooppuntgegevens op schaal
  - Aanzienlijke prestatieverbetering

U azure resource beheersjablonen ook gebruiken met Azure Desired State Configuration (DSC)-extensie om configuraties naar uw Azure VM's te pushen. De Azure DSC-extensie maakt gebruik van het Azure VM Agent-framework voor het leveren, uitvoeren en rapporteren over DSC-configuraties die worden uitgevoerd op Azure VM's. Zie [Extensie gewenste statusconfiguratie met Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details)voor compilatiedetails. 

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Een DSC-configuratie samenstellen in Azure-statusconfiguratie

### <a name="portal"></a>Portal

1. Klik in uw Automatiseringsaccount op **Configuratie (DSC)** op Statusconfiguratie .
1. Klik op het tabblad **Configuraties** en klik vervolgens op de configuratienaam die u wilt compileren.
1. Klik **op Compileren**.
1. Als de configuratie geen parameters heeft, wordt u gevraagd om te bevestigen of u deze wilt compileren. Als de configuratie parameters heeft, wordt het blad **Configuratie compileren** geopend, zodat u parameterwaarden opgeven.
1. De pagina Compilatietaak wordt geopend, zodat u de status van compilatietaak bijhouden. U deze pagina ook gebruiken om de knooppuntconfiguraties (MOF-configuratiedocumenten) te volgen die op de pull-server azure automation state configuration zijn geplaatst.

### <a name="azure-powershell"></a>Azure PowerShell

U [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) gebruiken om te beginnen met het compileren met Windows PowerShell. De volgende voorbeeldcode begint met het samenstellen van een DSC-configuratie genaamd **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob`retourneert een compilatietaakobject dat u gebruiken om de taakstatus bij te houden. U dit compilatietaakobject vervolgens gebruiken met [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) om de status van de compilatietaak te bepalen en [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) om de streams (uitvoer) weer te geven. In het volgende voorbeeld wordt gestart met de compilatie van de SampleConfig-configuratie, wordt gewacht tot deze is voltooid en worden de streams weergegeven.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Basisparameters declareren

Parameterdeclaratie in DSC-configuraties, inclusief parametertypen en -eigenschappen, werkt hetzelfde als in Azure Automation-runbooks. Zie [Een runbook starten in Azure Automation](automation-starting-a-runbook.md) voor meer informatie over runbook-parameters.

In het `FeatureName` volgende `IsPresent` voorbeeld worden de waarden van eigenschappen in de configuratie **parametersExample.sample** node die tijdens de compilatie worden gegenereerd, gebruikt en parameters gebruikt.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

U DSC-configuraties compileren die basisparameters gebruiken in de azure automation state configuration portal of met Azure PowerShell.

#### <a name="portal"></a>Portal

In de portal u parameterwaarden invoeren nadat u op Compileren hebt **geklikt.**

![Parameters voor het compileren van configuratie](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell vereist parameters in een [hashtable,](/powershell/module/microsoft.powershell.core/about/about_hash_tables)waarbij de sleutel overeenkomt met de parameternaam en de waarde gelijk is aan de parameterwaarde.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Zie `PSCredential` [Referentie-elementen](#credential-assets)voor informatie over het doorgeven van objecten als parameters .

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Configuraties compileren die samengestelde resources bevatten in Azure Automation

Met de functie **Samengestelde resources** u DSC-configuraties gebruiken als geneste bronnen in een configuratie. Met deze functie u meerdere configuraties op één resource toepassen. Zie [Samengestelde resources: een DSC-configuratie gebruiken als resource](/powershell/scripting/dsc/resources/authoringresourcecomposite) voor meer informatie over samengestelde resources.

> [!NOTE]
> Om ervoor te zorgen dat configuraties met samengestelde resources correct worden samengesteld, moet u eerst alle DSC-resources waarop de composieten vertrouwen, importeren in Azure Automation. Het toevoegen van een DSC-samengestelde resource is niet anders dan het toevoegen van een PowerShell-module aan Azure Automation. Dit proces wordt gedocumenteerd in [Modules beheren in Azure Automation.](/azure/automation/shared-resources/modules)

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>ConfigurationData beheren bij het samenstellen van configuraties in Azure Automation

**ConfigurationData** stelt u in staat om structurele configuratie te scheiden van elke omgevingsspecifieke configuratie tijdens het gebruik van PowerShell DSC. Zie ["Wat" scheiden van "Waar" in PowerShell DSC voor](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/)meer informatie.

> [!NOTE]
> Bij het opstellen in Azure Automation State Configuration u **ConfigurationData** gebruiken in Azure PowerShell, maar niet in de Azure-portal.

In het volgende voorbeeld maakt DSC-configuratie gebruik **van ConfigurationData** via de `$ConfigurationData` trefwoorden en `$AllNodes` trefwoorden. Hiervoor heb je ook de [xWebAdministration module](https://www.powershellgallery.com/packages/xWebAdministration/) nodig.

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

U de vorige DSC-configuratie compileren met Windows PowerShell. In het volgende script worden twee knooppuntconfiguraties toegevoegd aan de azure automation state configuration pull-service: **ConfigurationDataSample.MyVM1** en **ConfigurationDataSample.MyVM3**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Werken met assets in Azure Automation tijdens het maken van

Assetreferenties zijn hetzelfde in zowel Azure Automation State Configuration als runbooks. Raadpleeg de volgende artikelen voor meer informatie:

- [Certificaten](automation-certificates.md)
- [Verbindingen](automation-connections.md)
- [Referenties](automation-credentials.md)
- [Variabelen](automation-variables.md)

#### <a name="credential-assets"></a>Referentie-elementen

DSC-configuraties in Azure Automation kunnen verwijzen `Get-AutomationPSCredential` naar automatiseringsreferentie-elementen met behulp van de cmdlet. Als een configuratie een parameter `PSCredential` heeft `Get-AutomationPSCredential` die een object opgeeft, gebruikt u de tekenreeksnaam van een azure automation-referentieactief door te geven aan de cmdlet om de referentie op te halen. Maak vervolgens gebruik van dat object `PSCredential` voor de parameter die het object vereist. Achter de schermen wordt de Azure Automation-referentieasset met die naam opgehaald en doorgegeven aan de configuratie. In het onderstaande voorbeeld wordt dit scenario in actie weergegeven.

Om referenties veilig te houden in knooppuntconfiguraties (MOF-configuratiedocumenten) moet de referenties in het MOF-bestand voor knooppuntconfiguratie worden versleuteld. Momenteel moet u PowerShell DSC toestemming geven om referenties uit te geven in platte tekst tijdens de MOF-generatie van knooppuntconfiguratie. PowerShell DSC is zich er niet van bewust dat Azure Automation het hele MOF-bestand na de productie versleutelt via een compilatietaak.

U PowerShell DSC vertellen dat het goed is dat referenties in platte tekst worden uitgevoerd in de gegenereerde knooppuntconfiguratie-MOFs met behulp van configuratiegegevens. U moet `PSDscAllowPlainTextPassword = $true` via **ConfigurationData** voor elke knooppuntbloknaam die in de DSC-configuratie wordt weergegeven en referenties gebruikt, doorgeven.

In het volgende voorbeeld wordt een DSC-configuratie weergegeven die gebruikmaakt van een automatiseringsreferentie-asset.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

U de vorige DSC-configuratie compileren met PowerShell. Met de volgende PowerShell-code worden twee knooppuntconfiguraties toegevoegd aan de pull-server azure automation state configuration: **CredentialSample.MyVM1** en **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Wanneer de compilatie is voltooid, `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` ontvangt u mogelijk het foutbericht U dit bericht veilig negeren.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Uw DSC-configuratie samenstellen in Windows PowerShell

U ook knooppuntconfiguraties (MOF-bestanden) importeren die buiten Azure zijn gecompileerd. De import omvat compilatie van een ontwikkelaarswerkstation of in een service zoals [Azure DevOps.](https://dev.azure.com) Deze aanpak heeft meerdere voordelen, waaronder prestaties en betrouwbaarheid.

Compileren in Windows PowerShell biedt ook de mogelijkheid om configuratie-inhoud te ondertekenen. De DSC-agent verifieert een ondertekende knooppuntconfiguratie lokaal op een beheerd knooppunt. Verificatie zorgt ervoor dat de configuratie die op het knooppunt wordt toegepast, afkomstig is van een geautoriseerde bron.

> [!NOTE]
> Een knooppuntconfiguratiebestand mag niet groter zijn dan 1 MB om Azure Automation toe te staan het te importeren.

Zie [Verbeteringen in WMF 5.1 - Configuratie en module ondertekenen](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)voor meer informatie over het ondertekenen van knooppuntconfiguraties.

### <a name="compile-the-dsc-configuration"></a>De DSC-configuratie compileren

Het proces voor het compileren van DSC-configuraties in Windows PowerShell is opgenomen in de PowerShell DSC-documentatie [Schrijf, Compileren en Toepassen van een configuratie.](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)
U dit proces uitvoeren vanaf een werkstation voor ontwikkelaars of binnen een buildservice, zoals [Azure DevOps.](https://dev.azure.com) U vervolgens de MOF-bestanden importeren die zijn geproduceerd door de configuratie samen te stellen in de Azure State Configuration-service.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Een knooppuntconfiguratie importeren in de Azure-portal

1. Klik in uw Automatiseringsaccount op **Configuratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina Statusconfiguratie (DSC) op het tabblad **Configuraties** en klik vervolgens op **Toevoegen**.
1. Klik op de pagina Importeren op het mappictogram naast het tekstvak **Knooppuntconfiguratiebestand** om te zoeken naar een knooppuntconfiguratiebestand (MOF) op uw lokale computer.

   ![Zoeken naar lokaal bestand](./media/automation-dsc-compile/import-browse.png)

1. Voer een naam in het veld **Configuratienaam** in. Deze naam moet overeenkomen met de naam van de configuratie waaruit de knooppuntconfiguratie is samengesteld.
1. Klik op **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Een knooppuntconfiguratie importeren met Azure PowerShell

U de [cmdlet Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) gebruiken om een knooppuntconfiguratie in uw automatiseringsaccount te importeren.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Aan de slag met Azure Automation State Configuration](automation-dsc-getting-started.md)om aan de slag te gaan.
- Zie [Configuraties compileren in Azure Automation State Configuration](automation-dsc-compile.md)voor meer informatie over het samenstellen van DSC-configuraties, zodat u ze toewijzen aan doelknooppunten.
- Zie [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)voor een PowerShell-cmdletreferentie.
- Zie Azure Automation State Configuration Pricing voor prijsinformatie voor [prijsinformatie.](https://azure.microsoft.com/pricing/details/automation/)
- Zie [Continue implementatie naar virtuele machines met Azure Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md)als u een voorbeeld wilt zien van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.
