---
title: Computers configureren met een gewenste status in Azure Automation
description: In dit artikel leest u hoe u computers configureert met een gewenste status met behulp van Azure Automation status configuratie.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 93fb896dfc373a7402bbb3d1a38a655088d27fdf
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83735904"
---
# <a name="configure-machines-to-a-desired-state"></a>Machines configureren met een gewenste status

Met de configuratie van Azure Automation status kunt u configuraties voor uw servers opgeven en ervoor zorgen dat deze servers gedurende een bepaalde periode de opgegeven status hebben.

> [!div class="checklist"]
> - Een virtuele machine onboarden om te worden beheerd door Azure Automation DSC
> - Een configuratie uploaden naar Azure Automation
> - Een configuratie in een knooppunt configuratie compileren
> - Een knooppunt configuratie toewijzen aan een beheerd knoop punt
> - De nalevings status van een beheerd knoop punt controleren

Voor deze zelf studie gebruiken we een eenvoudige [DSC-configuratie](/powershell/scripting/dsc/configurations/configurations) die ervoor zorgt dat IIS op de virtuele machine is geïnstalleerd.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- Een Azure Automation-account. Zie [Azure Uitvoeren-als-account](automation-sec-configure-azure-runas-account.md) voor instructies over het maken van een Azure Automation Uitvoeren-als-account.
- Een Azure Resource Manager-VM (niet klassiek) met Windows Server 2008 R2 of hoger. Zie [uw eerste virtuele Windows-machine maken in de Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)voor instructies voor het maken van een VM.
- Azure PowerShell module versie 3,6 of hoger. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/azurerm/install-azurerm-ps).
- Vertrouwdheid met de desired state Configuration (DSC). Zie [Windows Power shell desired state Configuration Overview](/powershell/scripting/dsc/overview/overview)(Engelstalig) voor meer informatie over DSC.

## <a name="support-for-partial-configurations"></a>Ondersteuning voor gedeeltelijke configuraties

Azure Automation status configuratie ondersteunt het gebruik van [gedeeltelijke configuraties](/powershell/scripting/dsc/pull-server/partialconfigs). In dit scenario is DSC geconfigureerd om meerdere configuraties onafhankelijk te beheren en elke configuratie wordt opgehaald uit Azure Automation. Er kan echter slechts één configuratie worden toegewezen aan een knoop punt per Automation-account. Dit betekent dat als u twee configuraties voor een knoop punt gebruikt, twee Automation-accounts nodig zijn.

Zie de documentatie voor [gedeeltelijke configuraties](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)voor meer informatie over het registreren van een gedeeltelijke configuratie van een pull-service.

Zie [inzicht in de rol van DSC in een CI/cd-pijp lijn](/powershell/scripting/dsc/overview/authoringadvanced)voor meer informatie over hoe teams kunnen samen werken om servers gezamenlijk te beheren met configuratie als code.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij uw Azure-abonnement met de cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Een configuratie maken en uploaden naar Azure Automation


Typ het volgende in een tekst editor en sla deze lokaal op als **TestConfig. ps1**.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> In meer geavanceerde scenario's waarin u wilt dat meerdere modules worden geïmporteerd die DSC-resources bieden, moet u ervoor zorgen dat elke module een unieke `Import-DscResource` regel bevat in uw configuratie.

Roep de cmdlet [import-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) aan om de configuratie te uploaden naar uw Automation-account.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Een configuratie in een knooppunt configuratie compileren

Een DSC-configuratie moet worden gecompileerd in een knooppunt configuratie voordat deze aan een knoop punt kan worden toegewezen. Zie [DSC-configuraties](/powershell/scripting/dsc/configurations/configurations).

Roep de cmdlet [Start-AzAutomationDscCompilationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) om de configuratie te compileren `TestConfig` in een knooppunt configuratie `TestConfig.WebServer` met de naam in uw Automation-account.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Een virtuele machine registreren die door de status configuratie moet worden beheerd

U kunt Azure Automation status configuratie gebruiken voor het beheren van virtuele Azure-machines (zowel klassieke als Resource Manager), on-premises Vm's, Linux-machines, AWS Vm's en on-premises fysieke machines. In dit onderwerp wordt beschreven hoe u alleen Azure Resource Manager Vm's kunt registreren. Zie voor meer informatie over het registreren van andere typen machines [onboarding-machines voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md).

Roep de cmdlet [REGI ster-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) aan om uw virtuele machine bij Azure Automation status configuratie te registreren als een beheerd knoop punt. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Configuratie modus instellingen opgeven

Gebruik de cmdlet [REGI ster-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) om een virtuele machine te registreren als een beheerd knoop punt en configuratie-eigenschappen op te geven. U kunt bijvoorbeeld opgeven dat de status van de machine slechts één keer moet worden toegepast door `ApplyOnly` de waarde van de eigenschap op te geven `ConfigurationMode` . Status configuratie probeert de configuratie niet toe te passen na de eerste controle.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

U kunt ook opgeven hoe vaak DSC de configuratie status controleert met behulp van de- `ConfigurationModeFrequencyMins` eigenschap. Zie [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)(Engelstalig) voor meer informatie over DSC-configuratie-instellingen.

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Een knooppunt configuratie toewijzen aan een beheerd knoop punt

Nu kunnen we de gecompileerde knooppunt configuratie toewijzen aan de VM die u wilt configureren.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Hiermee wijst u de knooppunt configuratie `TestConfig.WebServer` met de naam toe aan het geregistreerde DSC-knoop punt `DscVm` . Het DSC-knoop punt wordt standaard om de 30 minuten gecontroleerd op naleving van de configuratie van het knoop punt. Zie [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)(Engelstalig) voor meer informatie over het wijzigen van het nalevings controle-interval.

## <a name="check-the-compliance-status-of-a-managed-node"></a>De nalevings status van een beheerd knoop punt controleren

U kunt met behulp van de cmdlet [Get-AzAutomationDscNodeReport](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0) rapporten ophalen over de nalevings status van een beheerd knoop punt.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Knoop punten uit de service verwijderen

Wanneer u een knoop punt toevoegt aan Azure Automation status configuratie, worden de instellingen in lokale Configuration Manager ingesteld om u te registreren bij de service en pull-configuraties en de vereiste modules om de computer te configureren.
Als u ervoor kiest om het knoop punt uit de service te verwijderen, kunt u dit doen met behulp van de Azure Portal of de AZ-cmdlets.

> [!NOTE]
> Bij het ongedaan maken van de registratie van een knoop punt van de service worden alleen de lokale Configuration Manager instellingen ingesteld zodat het knoop punt niet langer verbinding maakt met de service.
> Dit heeft geen invloed op de configuratie die momenteel wordt toegepast op het knoop punt.
> Als u de huidige configuratie wilt verwijderen, gebruikt u de [Power shell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) of verwijdert u het lokale configuratie bestand (dit is de enige optie voor Linux-knoop punten).

### <a name="azure-portal"></a>Azure Portal

Klik in Azure Automation op **State Configuration (DSC)** in de inhouds opgave.
Klik vervolgens op **knoop punten** om de lijst met knoop punten weer te geven die zijn geregistreerd bij de service.
Klik op de naam van het knoop punt dat u wilt verwijderen.
Klik in de weer gave van het knoop punt dat wordt geopend op **registratie ongedaan maken**.

### <a name="powershell"></a>PowerShell

Als u de registratie van een knoop punt bij de configuratie service van Azure Automation State wilt opheffen met behulp van Power shell, volgt u de documentatie voor de cmdlet [unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met de configuratie van de Azure Automation-status](automation-dsc-getting-started.md)
* [Configuratie van Azure Automation status inschakelen](automation-dsc-onboarding.md)
* [Configuraties compileren in Azure Automation status configuratie](automation-dsc-compile.md)
* [Azure Automation status configuratie-cmdlets](/powershell/module/azurerm.automation/#automation)
* [Prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/)
- [Continue implementatie met chocolade instellen](automation-dsc-cd-chocolatey.md)
