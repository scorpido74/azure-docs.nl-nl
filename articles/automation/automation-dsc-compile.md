---
title: Configuraties compileren in de configuratie van de Azure Automation status
description: In dit artikel wordt beschreven hoe u desired state Configuration (DSC)-configuraties voor Azure Automation kunt compileren.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: a4bdd28d2ad8f692b561d414af15b90b1609bac4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462119"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>DSC-configuraties compileren in Azure Automation status configuratie

U kunt configuraties voor desired state Configuration (DSC) op twee manieren compileren met Azure Automation status configuratie: in Azure en in Windows Power shell. De volgende tabel helpt u te bepalen wanneer u welke methode moet gebruiken op basis van de kenmerken van elke:

- Azure status configuratie compilatie service
  - Methode voor beginners met interactieve gebruikers interface
   - Eenvoudig de taak status bijhouden

- Windows Powershell
  - Aanroepen vanuit Windows Power shell op het lokale werk station of de build-service
  - Integreren met de ontwikkelings test pijplijn
  - Complexe parameter waarden opgeven
  - Werken met knoop punt-en niet-knooppunt gegevens op schaal
  - Aanzienlijke prestatie verbetering

Zie [desired state Configuration extension with Azure Resource Manager Templates](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details)(Engelstalig) voor meer informatie over de compilatie.

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Een DSC-configuratie in de Azure-status configuratie compileren

### <a name="portal"></a>Portal

1. Klik in uw Automation-account op **State Configuration (DSC)** .
1. Klik op het tabblad **configuraties** en klik vervolgens op de naam van de configuratie die u wilt compileren.
1. Klik op **compileren**.
1. Als de configuratie geen para meters heeft, wordt u gevraagd om te bevestigen dat u deze wilt compileren. Als de configuratie para meters heeft, wordt de Blade **configuratie voor compileren** geopend, zodat u parameter waarden kunt opgeven.
1. De pagina compilatie taak wordt geopend, zodat u de status van de compilatie taak kunt volgen. U kunt deze pagina ook gebruiken om de knooppunt configuraties (MOF-configuratie documenten) bij te houden die door de taak worden geplaatst op de configuratie pull-server van de Azure Automation status.

### <a name="azure-powershell"></a>Azure PowerShell

U kunt [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) gebruiken om te beginnen met het compileren met Windows Power shell. Met de volgende voorbeeld code wordt de compilatie van een DSC-configuratie met de naam SampleConfig gestart.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**Start-AzAutomationDscCompilationJob** retourneert een compilatie taak object dat u kunt gebruiken om de status ervan bij te houden. U kunt dit compilatie taak object vervolgens gebruiken met [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) om de status van de compilatie taak te bepalen en met [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) om de streams weer te geven (uitvoer). Met de volgende voorbeeld code wordt de compilatie van de SampleConfig-configuratie gestart, wordt gewacht tot deze is voltooid en worden vervolgens de streams weer gegeven.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Algemene para meters declareren

Parameter declaraties in DSC-configuraties, inclusief parameter typen en eigenschappen, werken hetzelfde als in Azure Automation runbooks. Zie [een Runbook starten in azure Automation](automation-starting-a-runbook.md) voor meer informatie over runbook-para meters.

In het volgende voor beeld worden twee para meters, **functie** naam en **IsPresent**, gebruikt om de waarden te bepalen van eigenschappen in de **ParametersExample. voorbeeld** knooppunt configuratie, gegenereerd tijdens de compilatie.

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

U kunt DSC-configuraties compileren die basis parameters gebruiken in de configuratie portal van de Azure Automation status of met Azure PowerShell:

#### <a name="portal"></a>Portal

In de portal kunt u parameter waarden invoeren nadat u op **compileren**hebt geklikt.

![Configuratie compilatie parameters](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Power shell vereist para meters in een [hashtabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables) waarbij de sleutel overeenkomt met de parameter naam en de waarde gelijk is aan de waarde van de para meter.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Zie [referentie-assets](#credential-assets) hieronder voor informatie over het door geven van PSCredentials als para meters.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Configuraties met samengestelde resources in Azure Automation compileren

Met de functie **samengestelde resources** kunt u DSC-configuraties gebruiken als geneste resources binnen een configuratie. Hierdoor kunt u meerdere configuraties Toep assen op één resource. Zie [Composite resources: een DSC-configuratie gebruiken als resource](/powershell/scripting/dsc/resources/authoringresourcecomposite) voor meer informatie over samengestelde resources.

> [!NOTE]
> Voor configuraties met samengestelde resources om correct te kunnen compileren, moet u er eerst voor zorgen dat alle DSC-resources waarvan de samen stelling afhankelijk is, in Azure Automation worden geïmporteerd.

Het toevoegen van een DSC-samengestelde resource wijkt af van het toevoegen van een Power shell-module aan Azure Automation. Het proces wordt beschreven in [modules beheren in azure Automation](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>ConfigurationData beheren bij het compileren van configuraties in Azure Automation

Met de functie **ConfigurationData** kunt u de structurele configuratie van elke omgeving-specifieke configuratie scheiden terwijl u Power shell DSC gebruikt. Zie [' What ' van ' where ' in Power shell DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) voor meer informatie over ConfigurationData.

> [!NOTE]
> U kunt ConfigurationData gebruiken bij het compileren in Azure Automation status configuratie met behulp van Azure PowerShell, maar niet in de Azure Portal.

Het volgende voor beeld van een DSC-configuratie maakt gebruik van ConfigurationData via de sleutel woorden $ConfigurationData en $AllNodes. U hebt ook de [module xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) nodig voor dit voor beeld:

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

U kunt de voor gaande DSC-configuratie compileren met Windows Power shell. Met het volgende script worden twee knooppunt configuraties aan de pull-service van de Azure Automation status configuratie toegevoegd: ConfigurationDataSample. MyVM1 en ConfigurationDataSample. MyVM3.

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Werken met assets in Azure Automation tijdens de compilatie

Activa verwijzingen zijn hetzelfde in de Azure Automation status configuratie en runbooks. Zie het volgende voor meer informatie:

- [Certificaten](automation-certificates.md)
- [Verbindingen](automation-connections.md)
- [Referenties](automation-credentials.md)
- [Variabelen](automation-variables.md)

#### <a name="credential-assets"></a>Referentie-assets

DSC-configuraties in Azure Automation kunnen verwijzen naar Automation-referentie assets met behulp van de cmdlet **Get-AutomationPSCredential** . Als een configuratie een para meter heeft die een PSCredential-type heeft, kunt u **Get-AutomationPSCredential** gebruiken door door te geven in de naam van de teken reeks van een Azure Automation referentie-element om de referentie op te halen. U kunt dit object vervolgens gebruiken voor de para meter die het PSCredential-object vereist. Achter de schermen wordt het Azure Automation referentie-element met die naam opgehaald en door gegeven aan de configuratie. In het onderstaande voor beeld wordt deze bewerking in actie weer gegeven.

Voor het beveiligen van referenties in een knooppunt configuratie moet u de referenties in het MOF-bestand van de knooppunt configuratie versleutelen. U moet Power shell DSC specifiek informeren over de machtiging om referenties uit te voeren in tekst zonder opmaak tijdens het genereren van de configuratie van de knoop punten. Power shell DSC weet niet dat Azure Automation het hele MOF-bestand versleutelt nadat het is gegenereerd via een compilatie taak.

Als u Power shell DSC-machtiging wilt geven voor het uitvoeren van referenties in tekst zonder opmaak in het gegenereerde knoop punt configuratie Mof's met configuratie gegevens, geeft u `PSDscAllowPlainTextPassword = $true`door. U kunt deze informatie door geven via ConfigurationData voor elke knooppunt blok naam die wordt weer gegeven in de DSC-configuratie en referenties gebruikt.

In het volgende voor beeld ziet u een DSC-configuratie die gebruikmaakt van een Automation-referentie-Asset.

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

U kunt de voor gaande DSC-configuratie compileren met Power shell. 

De volgende Power shell voegt twee knooppunt configuraties toe aan de Azure Automation status configuratie pull-server: CredentialSample. MyVM1 en CredentialSample. MyVM2.

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

>[!NOTE]
>Wanneer de compilatie is voltooid, ontvangt u mogelijk een fout melding: **de module micro soft. Power shell. Management is niet geïmporteerd, omdat de module micro soft. Power shell. Management al is geïmporteerd.** U kunt deze waarschuwing negeren.

## <a name="compiling-a-dsc-configuration-in-windows-powershell"></a>Een DSC-configuratie in Windows Power shell compileren

Het proces voor het compileren van DSC-configuraties in Windows Power shell is opgenomen in de Power shell DSC-documentatie [schrijven, compileren en Toep assen van een configuratie](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
U kunt het proces uitvoeren vanaf een ontwikkel werkstation of binnen een build-service, zoals [Azure DevOps](https://dev.azure.com).
U kunt de Mof's voor de resulterende knooppunt configuraties rechtstreeks importeren in de Azure-status configuratie service. 

>[!NOTE]
>Een configuratie bestand voor een knoop punt mag niet groter zijn dan 1 MB zodat het kan worden geïmporteerd in Azure Automation.

U kunt ook knooppunt configuraties (Mof's) importeren die buiten Azure zijn gecompileerd. Er zijn meerdere voor delen ten opzichte van deze benadering, met inbegrip van prestaties en betrouw baarheid.

Compileren in Windows Power shell biedt de mogelijkheid om configuratie-inhoud te ondertekenen, waarbij de DSC-agent een ondertekende knooppunt configuratie lokaal op een beheerd knoop punt verifieert. Verificatie zorgt ervoor dat de configuratie die wordt toegepast op het knoop punt afkomstig is van een geautoriseerde bron. Zie voor meer informatie over het ondertekenen van knooppunt configuraties [verbeteringen in WMF 5,1-configuratie en module ondertekenen](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Een knooppunt configuratie in de Azure Portal importeren

1. Klik in uw Automation-account op **State Configuration (DSC)** onder **configuratie beheer**.
1. Klik op de pagina status configuratie (DSC) op het tabblad **configuraties** en klik vervolgens op **+ toevoegen**.
1. Klik op de pagina importeren op het mappictogram naast het tekstvak **knooppunt configuratie bestand** om te bladeren naar een knooppunt configuratie bestand (MOF) op de lokale computer.

   ![Bladeren naar lokaal bestand](./media/automation-dsc-compile/import-browse.png)

1. Voer een naam in het tekstvak **configuratie naam** in. Deze naam moet overeenkomen met de naam van de configuratie waaruit de knooppunt configuratie is gecompileerd.
1. Klik op **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Een knooppunt configuratie met Azure PowerShell importeren

U kunt de cmdlet [import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) gebruiken om een knooppunt configuratie te importeren in uw Automation-account.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Volgende stappen

- Zie aan de slag [met de configuratie van de Azure Automation-status](automation-dsc-getting-started.md)om aan de slag te gaan.
- Zie [configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md)voor meer informatie over het compileren van DSC-configuraties zodat u ze aan doel knooppunten kunt toewijzen.
- Zie [Azure Automation status configuratie-cmdlets](/powershell/module/az.automation)voor informatie over de Power shell-cmdlet.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/)voor prijs informatie.
- Voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie raadpleegt u [continue implementatie met behulp van Azure Automation-status configuratie en Choco lade](automation-dsc-cd-chocolatey.md).
