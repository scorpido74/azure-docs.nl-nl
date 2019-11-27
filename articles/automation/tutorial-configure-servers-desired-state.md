---
title: Servers configureren met een gewenste status en afwijkingen beheren met Azure Automation
description: Zelf studie-server configuraties beheren met Azure Automation status configuratie
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 72e5018dc1212e57dc190c05cc54158d37ca7fe1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231497"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Servers configureren met een gewenste status en drift beheren

Met de configuratie van Azure Automation status kunt u configuraties voor uw servers opgeven en ervoor zorgen dat deze servers gedurende een bepaalde periode de opgegeven status hebben.

> [!div class="checklist"]
> - Een virtuele machine onboarden om te worden beheerd door Azure Automation DSC
> - Een configuratie uploaden naar Azure Automation
> - Een configuratie in een knooppunt configuratie compileren
> - Een knooppunt configuratie toewijzen aan een beheerd knoop punt
> - De nalevings status van een beheerd knoop punt controleren

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Een Azure Automation-account. Zie [Azure Uitvoeren-als-account](automation-sec-configure-azure-runas-account.md) voor instructies over het maken van een Azure Automation Uitvoeren-als-account.
- Een Azure Resource Manager-VM (niet klassiek) met Windows Server 2008 R2 of hoger. Zie [Uw eerste virtuele Windows-machine maken met behulp van Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) voor instructies voor het maken van een VM
- Azure PowerShell module versie 3,6 of hoger. Voer `Get-Module -ListAvailable AzureRM` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/azurerm/install-azurerm-ps).
- Vertrouwdheid met de desired state Configuration (DSC). Zie [Windows Power shell desired state Configuration Overview](/powershell/scripting/dsc/overview/overview) (Engelstalig) voor meer informatie over DSC

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzureRmAccount` en volg de instructies op het scherm.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Een configuratie maken en uploaden naar Azure Automation

Voor deze zelf studie gebruiken we een eenvoudige DSC-configuratie die ervoor zorgt dat IIS op de virtuele machine is geïnstalleerd.

Zie [DSC-configuraties](/powershell/scripting/dsc/configurations/configurations) voor meer informatie over DSC-configuraties.

Typ het volgende in een teksteditor en sla het bestand lokaal op als `TestConfig.ps1`.

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
> In meer geavanceerde scenario's waarin u wilt dat meerdere modules worden geïmporteerd die DSC-resources bieden, moet u ervoor zorgen dat elke module een unieke `Import-DscResource`-regel bevat in uw configuratie.

Roep de `Import-AzureRmAutomationDscConfiguration`-cmdlet aan om de configuratie te uploaden naar uw Automation-account:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Een configuratie in een knooppunt configuratie compileren

Een DSC-configuratie moet worden gecompileerd in een knooppunt configuratie voordat deze aan een knoop punt kan worden toegewezen.

Zie [DSC-configuraties](/powershell/scripting/dsc/configurations/configurations)voor meer informatie over het compileren van configuraties.

Roep de `Start-AzureRmAutomationDscCompilationJob`-cmdlet aan om de `TestConfig` configuratie in een knooppunt configuratie te compileren:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Hiermee maakt u een knooppunt configuratie met de naam `TestConfig.WebServer` in uw Automation-account.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Een virtuele machine registreren die door de status configuratie moet worden beheerd

U kunt Azure Automation status configuratie gebruiken voor het beheren van virtuele Azure-machines (zowel klassieke als Resource Manager), on-premises Vm's, Linux-machines, AWS Vm's en on-premises fysieke machines. In dit onderwerp wordt beschreven hoe u alleen Azure Resource Manager Vm's kunt registreren. Zie voor meer informatie over het registreren van andere typen machines [onboarding-machines voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md).

Roep de `Register-AzureRmAutomationDscNode`-cmdlet aan om uw VM te registreren bij Azure Automation status configuratie.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Hiermee wordt de opgegeven virtuele machine geregistreerd als een beheerd knoop punt in de status configuratie.

### <a name="specify-configuration-mode-settings"></a>Configuratie modus instellingen opgeven

Wanneer u een virtuele machine als een beheerd knoop punt registreert, kunt u ook eigenschappen van de configuratie opgeven. U kunt bijvoorbeeld opgeven dat de status van de machine slechts één keer moet worden toegepast (DSC probeert de configuratie niet toe te passen na de eerste controle) door `ApplyOnly` op te geven als de waarde van de eigenschap **ConfigurationMode** :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

U kunt ook opgeven hoe vaak DSC de configuratie status controleert met behulp van de eigenschap **ConfigurationModeFrequencyMins** :

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Zie [REGI ster-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)voor meer informatie over het instellen van configuratie-eigenschappen voor een beheerd knoop punt.

Zie [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)(Engelstalig) voor meer informatie over DSC-configuratie-instellingen.

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Een knooppunt configuratie toewijzen aan een beheerd knoop punt

Nu kunnen we de gecompileerde knooppunt configuratie toewijzen aan de VM die u wilt configureren.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Hiermee wordt de knooppunt configuratie met de naam `TestConfig.WebServer` toegewezen aan het geregistreerde DSC-knoop punt met de naam `DscVm`.
Het DSC-knoop punt wordt standaard om de 30 minuten gecontroleerd op naleving van de configuratie van het knoop punt.
Zie [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)(Engelstalig) voor meer informatie over het wijzigen van het nalevings controle-interval.

## <a name="working-with-partial-configurations"></a>Werken met gedeeltelijke configuraties

De configuratie van Azure Automation status ondersteunt het gebruik van [gedeeltelijke configuraties](/powershell/scripting/dsc/pull-server/partialconfigs).
In dit scenario is DSC geconfigureerd om meerdere configuraties onafhankelijk te beheren en elke configuratie wordt opgehaald uit Azure Automation.
Er kan echter slechts één configuratie worden toegewezen aan een knoop punt per Automation-account.
Dit betekent dat als u twee configuraties voor een knoop punt gebruikt, twee Automation-accounts nodig zijn.

Zie de documentatie voor [gedeeltelijke configuraties](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)voor meer informatie over het registreren van een gedeeltelijke configuratie van een pull-service.

Zie [inzicht in de rol van DSC in een CI/cd-pijp lijn](/powershell/scripting/dsc/overview/authoringadvanced)voor meer informatie over hoe teams kunnen samen werken om servers gezamenlijk te beheren met configuratie als code.

## <a name="check-the-compliance-status-of-a-managed-node"></a>De nalevings status van een beheerd knoop punt controleren

U kunt rapporten ophalen over de nalevings status van een beheerd knoop punt door de `Get-AzureRmAutomationDscNodeReport`-cmdlet aan te roepen:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Knoop punten uit de service verwijderen

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

- Zie aan de slag [met de configuratie van de Azure Automation-status](automation-dsc-getting-started.md) om aan de slag te gaan.
- Voor meer informatie over het voorbereiden van knoop punten, Zie [onboarding machines voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md)
- Zie [configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md) voor meer informatie over het compileren van DSC-configuraties zodat u ze aan doel knooppunten kunt toewijzen.
- Zie [Azure Automation status configuratie-cmdlets](/powershell/module/azurerm.automation/#automation) voor informatie over de Power shell-cmdlet.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/) voor prijs informatie.
- Voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie gaat u naar [continue implementatie met behulp van Azure Automation-status configuratie en chocolade](automation-dsc-cd-chocolatey.md)
