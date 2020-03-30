---
title: Servers configureren met een gewenste status en afwijkingen beheren met Azure Automation
description: Zelfstudie - Serverconfiguraties beheren met Azure Automation State Configuration
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 9e2f04f59a56be6c516eb90de45fdf7327673086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416595"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Servers configureren naar een gewenste status en drift beheren

Met Azure Automation State Configuration u configuraties voor uw servers opgeven en ervoor zorgen dat deze servers zich in de loop van de tijd in de opgegeven status bevinden.

> [!div class="checklist"]
> - Aan boord van een VM die wordt beheerd door Azure Automation DSC
> - Een configuratie uploaden naar Azure Automation
> - Een configuratie compileren in een knooppuntconfiguratie
> - Een knooppuntconfiguratie toewijzen aan een beheerd knooppunt
> - Controleer de nalevingsstatus van een beheerd knooppunt

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Een Azure Automation-account. Zie [Azure Uitvoeren-als-account](automation-sec-configure-azure-runas-account.md) voor instructies over het maken van een Azure Automation Uitvoeren-als-account.
- Een Azure Resource Manager VM (niet Classic) met Windows Server 2008 R2 of hoger. Zie [Uw eerste virtuele Windows-machine maken met behulp van Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) voor instructies voor het maken van een VM
- Azure PowerShell-module versie 3.6 of hoger. Voer `Get-Module -ListAvailable AzureRM` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/azurerm/install-azurerm-ps).
- Vertrouwdheid met gewenste statusconfiguratie (DSC). Zie [Overzicht van de gewenste configuratie van Windows PowerShell voor](/powershell/scripting/dsc/overview/overview) meer informatie over DSC

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzureRmAccount` en volg de instructies op het scherm.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Een configuratie maken en uploaden naar Azure Automation

Voor deze zelfstudie gebruiken we een eenvoudige DSC-configuratie die ervoor zorgt dat IIS op de VM is geïnstalleerd.

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
> In meer geavanceerde scenario's waarbij u meerdere modules moet importeren die DSC-bronnen bieden, moet u ervoor zorgen dat elke module een unieke `Import-DscResource` lijn in uw configuratie heeft.

Bel `Import-AzureRmAutomationDscConfiguration` de cmdlet om de configuratie te uploaden naar uw Automatiseringsaccount:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Een configuratie compileren in een knooppuntconfiguratie

Een DSC-configuratie moet worden gecompileerd in een knooppuntconfiguratie voordat deze aan een knooppunt kan worden toegewezen.

Zie [DSC-configuraties voor](/powershell/scripting/dsc/configurations/configurations)informatie over het samenstellen van configuraties.

Roep `Start-AzureRmAutomationDscCompilationJob` de cmdlet `TestConfig` aan om de configuratie samen te stellen in een knooppuntconfiguratie:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Hiermee wordt een knooppuntconfiguratie gemaakt met de naam `TestConfig.WebServer` in uw Automatiseringsaccount.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Een VM registreren om te worden beheerd door statusconfiguratie

U Azure Automation State Configuration gebruiken om Azure VM's (zowel Classic als Resource Manager), on-premises VM's, Linux-machines, AWS VM's en on-premises fysieke machines te beheren. In dit onderwerp behandelen we hoe u alleen Azure Resource Manager VM's registreert. Zie [Onboarding-machines voor beheer door Azure Automation State Configuration](automation-dsc-onboarding.md)voor informatie over het registreren van andere typen machines.

Bel `Register-AzureRmAutomationDscNode` de cmdlet om uw VM te registreren met Azure Automation State Configuration.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Hiermee wordt de opgegeven VM geregistreerd als een beheerd knooppunt in staatsconfiguratie.

### <a name="specify-configuration-mode-settings"></a>Instellingen voor de configuratiemodus opgeven

Wanneer u een VM registreert als een beheerd knooppunt, u ook eigenschappen van de configuratie opgeven. U bijvoorbeeld opgeven dat de status van de machine slechts één keer mag worden toegepast (DSC `ApplyOnly` probeert de configuratie niet toe te passen na de eerste controle) door op te geven als de waarde van de eigenschap **ConfigurationMode:**

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

U ook opgeven hoe vaak DSC de configuratiestatus controleert met de eigenschap **ConfigurationModeFrequencyMins:**

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Zie [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)voor meer informatie over het instellen van configuratie-eigenschappen voor een beheerd knooppunt.

Zie [Lokaal configuratiebeheer configureren](/powershell/scripting/dsc/managing-nodes/metaConfig)voor meer informatie over DSC-configuratie-instellingen.

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Een knooppuntconfiguratie toewijzen aan een beheerd knooppunt

Nu kunnen we de gecompileerde knooppuntconfiguratie toewijzen aan de VM die we willen configureren.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Hiermee wordt de knooppuntconfiguratie `TestConfig.WebServer` toegeschreven die is `DscVm`vernoemd naar het geregistreerde DSC-knooppunt met de naam .
Standaard wordt het DSC-knooppunt elke 30 minuten gecontroleerd op naleving van de knooppuntconfiguratie.
Zie Het [interval Lokale configuratiebeheer configureren](/powershell/scripting/dsc/managing-nodes/metaConfig)voor informatie over het wijzigen van het interval voor nalevingscontrole.

## <a name="working-with-partial-configurations"></a>Werken met gedeeltelijke configuraties

Azure Automation State Configuration ondersteunt het gebruik van [gedeeltelijke configuraties.](/powershell/scripting/dsc/pull-server/partialconfigs)
In dit scenario is DSC geconfigureerd om meerdere configuraties onafhankelijk te beheren en wordt elke configuratie opgehaald uit Azure Automation.
Er kan echter slechts één configuratie worden toegewezen aan een knooppunt per automatiseringsaccount.
Dit betekent dat als u twee configuraties voor een knooppunt gebruikt, u twee automatiseringsaccounts nodig hebt.

Zie de documentatie voor [gedeeltelijke configuraties](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)voor meer informatie over het registreren van een gedeeltelijke configuratie van pull-service.

Voor meer informatie over hoe teams kunnen samenwerken om servers gezamenlijk te beheren met behulp van configuratie als code zie [Inzicht in de rol van DSC in een CI/CD-pijplijn.](/powershell/scripting/dsc/overview/authoringadvanced)

## <a name="check-the-compliance-status-of-a-managed-node"></a>Controleer de nalevingsstatus van een beheerd knooppunt

U rapporten krijgen over de nalevingsstatus `Get-AzureRmAutomationDscNodeReport` van een beheerd knooppunt door de cmdlet aan te roepen:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Knooppunten verwijderen uit de service

Wanneer u een knooppunt toevoegt aan azure automation state configuration, worden de instellingen in Lokaal Configuratiebeheer ingesteld om te registreren bij de service en configuraties en vereiste modules op te halen om de machine te configureren.
Als u ervoor kiest het knooppunt uit de service te verwijderen, u dit doen met behulp van de Azure-portal of de Az-cmdlets.

> [!NOTE]
> Als u een knooppunt uitdeint voor de service, worden alleen de instellingen voor lokaal configuratiebeheer ingesteld, zodat het knooppunt geen verbinding meer maakt met de service.
> Dit heeft geen invloed op de configuratie die momenteel op het knooppunt wordt toegepast.
> Als u de huidige configuratie wilt verwijderen, gebruikt u de [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) of verwijdert u het lokale configuratiebestand (dit is de enige optie voor Linux-knooppunten).

### <a name="azure-portal"></a>Azure Portal

Klik vanuit Azure Automation op **Statusconfiguratie (DSC)** in de inhoudsopgave.
Klik vervolgens op **Knooppunten** om de lijst met knooppunten weer te geven die bij de service zijn geregistreerd.
Klik op de naam van het knooppunt dat u wilt verwijderen.
Klik in de knooppuntweergave die wordt geopend op **Aanmelden.**

### <a name="powershell"></a>PowerShell

Als u een knooppunt van Azure Automation State Configuration Service met PowerShell wilt uitschrijven, volgt u de documentatie voor de cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Volgende stappen

- Zie [Aan de slag met Azure Automation State Configuration](automation-dsc-getting-started.md) om aan de slag te gaan.
- Zie [Onboarding-machines voor beheer door Azure Automation State Configuration](automation-dsc-onboarding.md) voor meer informatie over het inschepen van knooppunten.
- Zie [Configuraties compileren in Azure Automation State Configuration](automation-dsc-compile.md) voor meer informatie over het samenstellen van DSC-configuraties, zodat u ze toewijzen aan doelknooppunten.
- Zie [Cmdlets Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) voor PowerShell-cmdlet
- Zie Azure [Automation State Configuration Pricing](https://azure.microsoft.com/pricing/details/automation/) voor prijsinformatie
- Zie [Continue implementatie met Azure Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md) voor een voorbeeld van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.
