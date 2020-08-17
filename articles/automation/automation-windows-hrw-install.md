---
title: Een Windows-Hybrid Runbook Worker implementeren in Azure Automation
description: In dit artikel wordt uitgelegd hoe u een Hybrid Runbook Worker implementeert die u kunt gebruiken om runbooks uit te voeren op Windows-computers in uw lokale Data Center of in de cloud omgeving.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 90b16f10cfa94ce427a9f6249c72842f8ef6278a
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270577"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Een Windows-Hybrid Runbook Worker implementeren

U kunt de functie Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks uit te voeren op de computer waarop de rol wordt gehost en aan resources in de omgeving om deze lokale resources te beheren. Azure Automation runbooks opslaat en beheert en vervolgens aan een of meer aangewezen computers worden geleverd. In dit artikel wordt beschreven hoe u een Hybrid Runbook Worker implementeert op een Windows-computer, hoe u de werk nemer verwijdert en hoe u een Hybrid Runbook Worker groep verwijdert.

Nadat u een runbook worker hebt geïmplementeerd, raadpleegt u [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md) voor meer informatie over het configureren van runbooks voor het automatiseren van processen in uw on-premises Data Center of een andere cloud omgeving.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u over het volgende beschikt.

### <a name="a-log-analytics-workspace"></a>Een Log Analytics-werk ruimte

De functie Hybrid Runbook Worker is afhankelijk van een Azure Monitor Log Analytics-werk ruimte om de rol te installeren en te configureren. U kunt dit maken via [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), via [Power shell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)of in de [Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Als u geen Azure Monitor Log Analytics-werk ruimte hebt, raadpleegt u de [ontwerp richtlijnen voor Azure monitor logboek](../azure-monitor/platform/design-logs-deployment.md) voordat u de werk ruimte maakt.

Als u een werk ruimte hebt, maar deze niet is gekoppeld aan uw Automation-account en u een automatiserings functie inschakelt, voegt u functionaliteit toe voor Azure Automation, inclusief ondersteuning voor de Hybrid Runbook Worker. Wanneer u een van de Azure Automation functies in uw Log Analytics-werk ruimte, met name [updatebeheer](update-management/update-mgmt-overview.md) of [Wijzigingen bijhouden en inventaris](change-tracking.md), inschakelt, worden de werk onderdelen automatisch naar de agent computer gepusht.

   Als u de functie Updatebeheer wilt toevoegen aan uw werk ruimte, voert u de volgende Power shell-cmdlet uit:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

   Voer de volgende Power shell-cmdlet uit om de functie Wijzigingen bijhouden en inventaris aan uw werk ruimte toe te voegen:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Log Analytics-agent

De Hybrid Runbook Worker-rol vereist de [log Analytics-agent](../azure-monitor/platform/log-analytics-agent.md) voor het ondersteunde Windows-besturings systeem.

### <a name="supported-windows-operating-system"></a>Ondersteund Windows-besturings systeem

De volgende versies van het Windows-besturings systeem worden officieel ondersteund voor een Windows-Hybrid Runbook Worker:

* Windows Server 2019
* Windows Server 2016, versie 1709 en 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enter prise (inclusief meerdere sessies) en Pro
* Windows 8 Enter prise en Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Minimale vereisten

De minimale vereisten voor een Windows-Hybrid Runbook Worker zijn:

* Windows Power shell 5,1 of hoger ([WMF 5,1 downloaden](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET framework 4.6.2 of hoger
* Twee kernen
* 4 GB aan RAM-geheugen
* Poort 443 (uitgaand)

### <a name="network-configuration"></a>Netwerkconfiguratie

Zie [uw netwerk configureren](automation-hybrid-runbook-worker.md#network-planning)voor meer informatie over de netwerk vereisten voor de Hybrid Runbook Worker.

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Een machine toevoegen aan een Hybrid Runbook Worker groep

U kunt de werk computer toevoegen aan een Hybrid Runbook Worker groep in uw Automation-account. Houd er rekening mee dat u Automation-runbooks moet ondersteunen als u hetzelfde account gebruikt voor zowel de Azure Automation-functie als de Hybrid Runbook Worker groepslid maatschap. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van de Hybrid Runbook Worker.

>[!NOTE]
>Als Azure Automation [updatebeheer](update-management/update-mgmt-overview.md) wordt ingeschakeld, wordt elke Windows-computer die is verbonden met uw log Analytics-werk ruimte automatisch geconfigureerd als Hybrid Runbook worker om de updates van het besturings systeem te kunnen beheren. Deze werk nemer is echter niet geregistreerd bij Hybrid Runbook Worker groepen die al zijn gedefinieerd in uw Automation-account.

## <a name="enabling-machines-for-management-with-azure-automation-state-configuration"></a>Computers inschakelen voor beheer met Azure Automation status configuratie

Zie voor meer informatie over het inschakelen van machines voor beheer met Azure Automation status configuratie [machines inschakelen voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md).

> [!NOTE]
> Als u de configuratie wilt beheren van machines die ondersteuning bieden voor de Hybrid Runbook Worker rol met behulp van desired state Configuration (DSC), moet u de computers als DSC-knoop punten toevoegen.

## <a name="windows-hybrid-runbook-worker-installation-options"></a>Installatie opties voor Windows Hybrid Runbook Worker

Als u een Windows-Hybrid Runbook Worker wilt installeren en configureren, kunt u een van de volgende methoden gebruiken.

* Voor virtuele machines van Azure installeert u de Log Analytics agent voor Windows met behulp [van de Virtual Machine-extensie voor Windows](../virtual-machines/extensions/oms-windows.md). Met de uitbrei ding wordt de Log Analytics agent geïnstalleerd op virtuele machines van Azure en worden virtuele machines geregistreerd in een bestaande Log Analytics-werk ruimte met behulp van een Azure Resource Manager-sjabloon of Power shell. Zodra de agent is geïnstalleerd, kan de virtuele machine worden toegevoegd aan een Hybrid Runbook Worker groep in uw Automation-account.

* Voor niet-Azure Vm's installeert u de Log Analytics-agent voor Windows met behulp van de implementatie opties die worden beschreven in het artikel [Windows-computers verbinden met Azure monitor](../azure-monitor/platform/agent-windows.md) . U kunt dit proces herhalen voor meerdere machines om meerdere werk nemers aan uw omgeving toe te voegen. Zodra de agent is geïnstalleerd, kunnen de virtuele machines worden toegevoegd aan een Hybrid Runbook Worker groep in uw Automation-account.

* Gebruik een meegeleverd Power shell-script om het proces van het configureren van een of meer Windows-computers volledig te [automatiseren](#automated-deployment) . Dit is de aanbevolen methode voor machines in uw Data Center of een andere cloud omgeving.

## <a name="automated-deployment"></a>Geautomatiseerde implementatie

Voer op de doel computer de volgende stappen uit om de installatie en configuratie van de functie Windows Hybrid Worker te automatiseren met behulp van het Power shell-script **New-OnPremiseHybridWorker.ps1**. Met het script voert u de volgende stappen uit:

* Installeert de benodigde modules
* Meld u aan met uw Azure-account
* Hiermee wordt gecontroleerd of de opgegeven resource groep en het Automation-account bestaan
* Hiermee maakt u verwijzingen naar de kenmerken van het Automation-account
* Hiermee wordt een Azure Monitor Log Analytics werk ruimte gemaakt als deze niet is opgegeven
* De Azure Automation oplossing in de werk ruimte inschakelen
* De Log Analytics-agent voor Windows downloaden en installeren
* Registreer de machine als Hybrid Runbook Worker

### <a name="step-1---download-the-powershell-script"></a>Stap 1: het Power shell-script downloaden

Down load het **New-OnPremiseHybridWorker.ps1** script van de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Nadat u het script hebt gedownload, kopieert of uitvoert u het op de doel computer. Het **New-OnPremiseHybridWorker.ps1** script maakt gebruik van de para meters die hieronder worden beschreven tijdens de uitvoering.

| Parameter | Status | Beschrijving |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Verplicht | De naam van de resource groep die is gekoppeld aan uw Automation-account. |
| `AutomationAccountName` | Verplicht | De naam van uw Automation-account.
| `Credential` | Optioneel | De referenties die moeten worden gebruikt wanneer u zich aanmeldt bij de Azure-omgeving. |
| `HybridGroupName` | Verplicht | De naam van een Hybrid Runbook Worker groep die u opgeeft als doel voor de runbooks die dit scenario ondersteunen. |
| `OMSResourceGroupName` | Optioneel | De naam van de resource groep voor de Log Analytics-werk ruimte. Als deze resource groep niet is opgegeven, wordt de waarde van `AAResourceGroupName` gebruikt. |
| `SubscriptionID` | Verplicht | De id van het Azure-abonnement dat is gekoppeld aan uw Automation-account. |
| `TenantID` | Optioneel | De id van de Tenant organisatie die aan uw Automation-account is gekoppeld. |
| `WorkspaceName` | Optioneel | De naam van de Log Analytics werkruimte. Als u geen Log Analytics-werk ruimte hebt, maakt en configureert het script een. |

> [!NOTE]
> Bij het inschakelen van functies ondersteunt Azure Automation alleen bepaalde regio's voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Stap 2: Windows Power shell-opdracht regel shell openen

Klik in het **menu Start** op **Start**, typ **Power shell**, klik met de rechter muisknop op **Windows Power shell**en klik vervolgens op **als administrator uitvoeren**.

### <a name="step-3---run-the-powershell-script"></a>Stap 3: het Power shell-script uitvoeren

Blader in de Power shell-opdracht regel shell naar de map die het script bevat dat u hebt gedownload. Wijzig de waarden voor de para meters,,,, `AutomationAccountName` `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` en `WorkspaceName` . Voer het script uit.

Nadat u het script hebt uitgevoerd, wordt u gevraagd om te verifiëren bij Azure. U moet zich aanmelden met een account dat lid is van de rol abonnements beheerders en mede beheerder van het abonnement.

```powershell-interactive
$NewOnPremiseHybridWorkerParameters = @{
  AutomationAccountName = <nameOfAutomationAccount>
  AAResourceGroupName   = <nameOfResourceGroup>
  OMSResourceGroupName  = <nameOfResourceGroup>
  HybridGroupName       = <nameOfHRWGroup>
  SubscriptionID        = <subscriptionId>
  WorkspaceName         = <nameOfLogAnalyticsWorkspace>
}
.\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
```

### <a name="step-4---install-nuget"></a>Stap 4-NuGet installeren

U wordt gevraagd om akkoord te gaan met de installatie van NuGet en om te verifiëren met uw Azure-referenties. Als u niet de nieuwste versie van NuGet hebt, kunt u deze downloaden via de [beschik bare NuGet-distributie versies](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Stap 5: de implementatie controleren

Nadat het script is voltooid, toont de pagina Hybrid Worker groepen in uw Automation-account de nieuwe groep en het aantal leden. Als het een bestaande groep is, wordt het aantal leden verhoogd. U kunt de groep selecteren in de lijst op de pagina Hybrid Worker groepen en de tegel **Hybrid Workers** kiezen. Op de pagina Hybrid Workers kunt u elk lid van de groep weer geven.

## <a name="manual-deployment"></a>Handmatige implementatie

Voer de volgende stappen uit om een Windows-Hybrid Runbook Worker te installeren en te configureren.

### <a name="step-1---verify-agent-is-reporting-to-workspace"></a>Stap 1: controleren of de agent wordt gerapporteerd aan de werk ruimte

De Log Analytics-agent voor Windows verbindt computers met een Azure Monitor Log Analytics-werk ruimte. Wanneer u de agent op de computer installeert en verbindt met uw werk ruimte, worden automatisch de onderdelen gedownload die vereist zijn voor de Hybrid Runbook Worker.

Wanneer de agent na een paar minuten verbinding heeft gemaakt met uw Log Analytics-werk ruimte, kunt u de volgende query uitvoeren om te controleren of er heartbeat-gegevens naar de werk ruimte worden verzonden.

```kusto
Heartbeat 
| where Category == "Direct Agent"
| where TimeGenerated > ago(30m)
```

In de zoek resultaten ziet u heartbeat-records voor de machine, waarmee wordt aangegeven dat deze is verbonden en dat er wordt gerapporteerd aan de service. Standaard stuurt elke agent een heartbeat-record naar de toegewezen werk ruimte. Gebruik de volgende stappen om de installatie van de agent en de installatie te volt ooien.

1. Schakel de functie in om de agent computer toe te voegen. Zie voor Updatebeheer-en Azure-Vm's [updatebeheer inschakelen vanuit een Automation-account](update-management/update-mgmt-enable-automation-account.md), updatebeheer in te [scha kelen door te bladeren door de Azure Portal](update-management/update-mgmt-enable-portal.md), [updatebeheer in te scha kelen vanuit een Runbook](update-management/update-mgmt-enable-runbook.md)of [updatebeheer in te scha kelen vanaf een Azure-VM](update-management/update-mgmt-enable-vm.md). Voor Wijzigingen bijhouden-en Azure-Vm's raadpleegt u [Azure Vm's inschakelen](automation-enable-changes-from-auto-acct.md#enable-azure-vms)en voor virtuele machines die niet van Azure zijn, Zie [machines inschakelen in de werk ruimte](automation-enable-changes-from-auto-acct.md#enable-machines-in-the-workspace).

2. Als u de versie van de Hybrid Runbook Worker wilt bevestigen, bladert u naar `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` de submap **versie** .

### <a name="step-2---install-the-runbook-environment-and-connect-to-azure-automation"></a>Stap 2: de runbook-omgeving installeren en verbinding maken met Azure Automation

Wanneer u een agent configureert om te rapporteren aan een Log Analytics-werk ruimte, wordt door de Azure Automation `HybridRegistration` -functie de Power shell-module met de cmdlet gepusht `Add-HybridRunbookWorker` . Gebruik deze cmdlet om de runbook-omgeving op de computer te installeren en te registreren bij Azure Automation.

Open een Power shell-sessie in de beheerders modus en voer de volgende opdrachten uit om de module te importeren.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Voer nu de `Add-HybridRunbookWorker` cmdlet uit met de volgende syntaxis.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
```

U kunt de informatie ophalen die vereist is voor de para meters `Url` en `Key` van de pagina **sleutels** in uw Automation-account. Selecteer **sleutels** onder de sectie **account instellingen** aan de linkerkant van de pagina.

![Pagina sleutels beheren](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* `Url`Kopieer de waarde voor **URL**voor de para meter.

* Kopieer voor de `Key` para meter de waarde voor de **primaire toegangs sleutel**.

* Gebruik de `GroupName` naam van de Hybrid Runbook worker groep voor de para meter. Als deze groep al bestaat in het Automation-account, wordt de huidige machine hieraan toegevoegd. Als deze groep niet bestaat, wordt deze toegevoegd.

* Stel, indien nodig, de `Verbose` para meter in om details over de installatie te ontvangen.

### <a name="step-3----install-powershell-modules"></a>Stap 3: Power shell-modules installeren

Runbooks kunnen gebruikmaken van de activiteiten en cmdlets die zijn gedefinieerd in de modules die in uw Azure Automation omgeving zijn geïnstalleerd. Omdat deze modules niet automatisch worden geïmplementeerd op on-premises machines, moet u ze hand matig installeren. De uitzonde ring hierop is de Azure-module. Deze module wordt standaard geïnstalleerd en biedt toegang tot cmdlets voor alle Azure-Services en-activiteiten voor Azure Automation.

Omdat het primaire doel van de Hybrid Runbook Worker is om lokale bronnen te beheren, moet u waarschijnlijk de modules installeren die deze bronnen ondersteunen, met name de `PowerShellGet` module. Zie [Windows Power shell](/powershell/scripting/developer/windows-powershell)voor meer informatie over het installeren van Windows Power shell-modules.

Modules die zijn geïnstalleerd, moeten zich bevinden in een locatie waarnaar wordt verwezen door de `PSModulePath` omgevings variabele, zodat de Hybrid worker deze automatisch kan importeren. Zie [Installing modules in PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)(Engelstalig) voor meer informatie.

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-machine"></a><a name="remove-windows-hybrid-runbook-worker"></a>De Hybrid Runbook Worker verwijderen van een on-premises Windows-computer

1. Ga in het Azure Portal naar uw Automation-account.

2. Onder **account instellingen**selecteert u **sleutels** en noteert u de waarden voor **URL** en **primaire toegangs sleutel**.

3. Open een Power shell-sessie in de beheerders modus en voer de volgende opdracht uit met uw URL en primaire toegangs sleutel waarden. Gebruik de `Verbose` para meter voor een gedetailleerd logboek van het verwijderings proces. Als u verouderde machines uit uw Hybrid Worker groep wilt verwijderen, gebruikt u de optionele `machineName` para meter.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Een Hybrid Worker-groep verwijderen

Als u een Hybrid Runbook Worker groep wilt verwijderen, moet u eerst de Hybrid Runbook Worker verwijderen van elke computer die lid is van de groep. Gebruik vervolgens de volgende stappen om de groep te verwijderen:

1. Open het Automation-account in de Azure Portal.

2. Selecteer **Hybrid worker groups** onder **Process Automation**. Selecteer de groep die u wilt verwijderen. De eigenschappen pagina voor die groep wordt weer gegeven.

   ![De pagina Eigenschappen](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Selecteer op de pagina eigenschappen voor de geselecteerde groep de optie **verwijderen**. Er wordt een bericht gevraagd om deze actie te bevestigen. Selecteer **Ja** als u zeker weet dat u wilt door gaan.

   ![Bevestigingsbericht](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Het kan enkele seconden duren voordat dit proces is voltooid. U kunt de voortgang bijhouden onder **Meldingen** in het menu.

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen te automatiseren in uw on-premises Data Center of andere cloud omgeving.

* Zie [problemen met Hybrid Runbook worker oplossen](troubleshoot/hybrid-runbook-worker.md#general)voor meer informatie over het oplossen van problemen met uw Hybrid Runbook Workers.
