---
title: Servers configureren met een gewenste status en afwijkingen beheren met Azure Automation
description: Zelfstudie - Serverconfiguraties beheren met Azure Automation State Configuration
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678698"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Servers configureren naar een gewenste status en drift beheren

Met Azure Automation State Configuration u configuraties voor uw servers opgeven en ervoor zorgen dat deze servers zich in de loop van de tijd in de opgegeven status bevinden.

> [!div class="checklist"]
> - Aan boord van een VM die wordt beheerd door Azure Automation DSC
> - Een configuratie uploaden naar Azure Automation
> - Een configuratie compileren in een knooppuntconfiguratie
> - Een knooppuntconfiguratie toewijzen aan een beheerd knooppunt
> - Controleer de nalevingsstatus van een beheerd knooppunt

Voor deze zelfstudie gebruiken we een eenvoudige [DSC-configuratie](/powershell/scripting/dsc/configurations/configurations) die ervoor zorgt dat IIS op de VM is geïnstalleerd.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- Een Azure Automation-account. Zie [Azure Uitvoeren-als-account](automation-sec-configure-azure-runas-account.md) voor instructies over het maken van een Azure Automation Uitvoeren-als-account.
- Een Azure Resource Manager VM (niet klassiek) met Windows Server 2008 R2 of hoger. Zie Uw eerste virtuele [Windows-machine maken in de Azure-portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)voor instructies voor het maken van een vm.
- Azure PowerShell-module versie 3.6 of hoger. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/azurerm/install-azurerm-ps).
- Vertrouwdheid met gewenste statusconfiguratie (DSC). Zie [overzicht van de gewenste configuratie van Windows PowerShell](/powershell/scripting/dsc/overview/overview)voor meer informatie over DSC.

## <a name="support-for-partial-configurations"></a>Ondersteuning voor gedeeltelijke configuraties

Azure Automation State Configuration ondersteunt het gebruik van [gedeeltelijke configuraties.](/powershell/scripting/dsc/pull-server/partialconfigs) In dit scenario is DSC geconfigureerd om meerdere configuraties onafhankelijk te beheren en wordt elke configuratie opgehaald uit Azure Automation. Er kan echter slechts één configuratie worden toegewezen aan een knooppunt per automatiseringsaccount. Dit betekent dat als u twee configuraties voor een knooppunt gebruikt, u twee automatiseringsaccounts nodig hebt.

Zie de documentatie voor [gedeeltelijke configuraties](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)voor meer informatie over het registreren van een gedeeltelijke configuratie van een pull-service.

Zie De [rol van DSC in een CI/CD-pijplijn begrijpen](/powershell/scripting/dsc/overview/authoringadvanced)voor meer informatie over hoe teams kunnen samenwerken om servers samen te beheren met configuratie als code.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij uw Azure-abonnement met de [cmdlet Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) en volg de aanwijzingen op het scherm.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Een configuratie maken en uploaden naar Azure Automation


Typ in een teksteditor het volgende en sla deze lokaal op als **TestConfig.ps1**.

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

Bel de cmdlet [Import-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) om de configuratie te uploaden naar uw Automatiseringsaccount.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Een configuratie compileren in een knooppuntconfiguratie

Een DSC-configuratie moet worden gecompileerd in een knooppuntconfiguratie voordat deze aan een knooppunt kan worden toegewezen. Zie [DSC-configuraties](/powershell/scripting/dsc/configurations/configurations).

Bel de [cmdlet Start-AzAutomationDscCompilationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) om de `TestConfig` configuratie `TestConfig.WebServer` samen te stellen in een knooppuntconfiguratie die is genoemd in uw Automatiseringsaccount.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Een VM registreren om te worden beheerd door statusconfiguratie

U Azure Automation State Configuration gebruiken om Azure VM's (zowel Classic als Resource Manager), on-premises VM's, Linux-machines, AWS VM's en on-premises fysieke machines te beheren. In dit onderwerp behandelen we hoe u alleen Azure Resource Manager VM's registreert. Zie [Onboarding-machines voor beheer door Azure Automation State Configuration](automation-dsc-onboarding.md)voor informatie over het registreren van andere typen machines.

Bel de [cmdlet Register-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) om uw VM te registreren met Azure Automation State Configuration als een beheerd knooppunt. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Instellingen voor de configuratiemodus opgeven

Gebruik de [cmdlet Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) om een VM als beheerd knooppunt te registreren en configuratie-eigenschappen op te geven. U bijvoorbeeld opgeven dat de status van de machine slechts `ApplyOnly` één keer `ConfigurationMode` moet worden toegepast door op te geven als de waarde van de eigenschap. Statusconfiguratie probeert de configuratie niet toe te passen na de eerste controle.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

U ook opgeven hoe vaak DSC `ConfigurationModeFrequencyMins` de configuratiestatus controleert met behulp van de eigenschap. Zie [Lokaal configuratiebeheer configureren](/powershell/scripting/dsc/managing-nodes/metaConfig)voor meer informatie over DSC-configuratie-instellingen.

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Een knooppuntconfiguratie toewijzen aan een beheerd knooppunt

Nu kunnen we de gecompileerde knooppuntconfiguratie toewijzen aan de VM die we willen configureren.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Hiermee wordt de knooppuntconfiguratie `TestConfig.WebServer` toegeschreven die is `DscVm`vernoemd naar het geregistreerde DSC-knooppunt . Standaard wordt het DSC-knooppunt elke 30 minuten gecontroleerd op naleving van de knooppuntconfiguratie. Zie Het [interval Lokale configuratiebeheer configureren](/powershell/scripting/dsc/managing-nodes/metaConfig)voor informatie over het wijzigen van het interval voor nalevingscontrole.

## <a name="check-the-compliance-status-of-a-managed-node"></a>Controleer de nalevingsstatus van een beheerd knooppunt

U rapporten krijgen over de nalevingsstatus van een beheerd knooppunt met behulp van de [cmdlet Get-AzAutomationDscNodeReport.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0)

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Knooppunten uit de service verwijderen

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

- Zie [Aan de slag met Azure Automation State Configuration](automation-dsc-getting-started.md)om aan de slag te gaan.
- Zie [Onboarding-machines voor beheer door Azure Automation State Configuration voor](automation-dsc-onboarding.md)meer informatie over het inschepen van knooppunten.
- Zie [Configuraties compileren in Azure Automation State Configuration](automation-dsc-compile.md)voor meer informatie over het samenstellen van DSC-configuraties, zodat u ze toewijzen aan doelknooppunten.
- Zie Cmdlets azure [automation state configuration](/powershell/module/azurerm.automation/#automation)voor PowerShell-cmdlet .
- Zie Azure Automation State Configuration Pricing voor prijsinformatie voor [prijsinformatie.](https://azure.microsoft.com/pricing/details/automation/)
- Zie [Continue implementatie met Azure Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md) voor een voorbeeld van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.
