---
title: Een Windows-Hybrid Runbook Worker implementeren in Azure Automation
description: In dit artikel wordt uitgelegd hoe u een Hybrid Runbook Worker implementeert die u kunt gebruiken om runbooks uit te voeren op Windows-computers in uw lokale Data Center of in de cloud omgeving.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 3a27cee7a94ee6f33c399d10f90e47ec574e7380
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744182"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Een Windows-Hybrid Runbook Worker implementeren

U kunt de functie Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks uit te voeren op de computer waarop de rol wordt gehost en aan resources in de omgeving om deze lokale resources te beheren. Azure Automation runbooks opslaat en beheert en vervolgens aan een of meer aangewezen computers worden geleverd. In dit artikel wordt beschreven hoe u een Hybrid Runbook Worker implementeert op een Windows-computer.

Nadat u een runbook worker hebt geïmplementeerd, raadpleegt u [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md) voor meer informatie over het configureren van runbooks voor het automatiseren van processen in uw on-premises Data Center of een andere cloud omgeving.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Installatie en configuratie van Windows Hybrid Runbook Worker

Als u een Windows-Hybrid Runbook Worker wilt installeren en configureren, kunt u een van de volgende methoden gebruiken.

* Voor virtuele machines van Azure installeert u de Log Analytics agent voor Windows met behulp [van de Virtual Machine-extensie voor Windows](../virtual-machines/extensions/oms-windows.md). Met de uitbrei ding wordt de Log Analytics agent geïnstalleerd op virtuele machines van Azure en worden virtuele machines geregistreerd in een bestaande Log Analytics-werk ruimte met behulp van een Azure Resource Manager-sjabloon of Power shell. Zodra de agent is geïnstalleerd, kan de virtuele machine worden toegevoegd aan een Hybrid Runbook Worker groep in uw Automation-account. Raadpleeg stap 3 en 4 in de sectie [hand matige implementatie](#manual-deployment) .

* Gebruik een Automation-runbook om het proces van het configureren van een Windows-computer volledig te automatiseren. Dit is de aanbevolen methode voor machines in uw Data Center of een andere cloud omgeving.

* Volg een stapsgewijze procedure voor het hand matig installeren en configureren van de Hybrid Runbook Worker-functie op uw niet-Azure-VM.

> [!NOTE]
> Als u de configuratie wilt beheren van servers die ondersteuning bieden voor de Hybrid Runbook Worker rol met behulp van desired state Configuration (DSC), moet u de servers als DSC-knoop punten toevoegen.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Minimale vereisten voor Windows-Hybrid Runbook Worker

De minimale vereisten voor een Windows-Hybrid Runbook Worker zijn:

* Windows Server 2012 of hoger
* Windows Power shell 5,1 of hoger ([WMF 5,1 downloaden](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET framework 4.6.2 of hoger
* Twee kernen
* 4 GB aan RAM-geheugen
* Poort 443 (uitgaand)

### <a name="network-configuration"></a>Netwerkconfiguratie

Zie [uw netwerk configureren](automation-hybrid-runbook-worker.md#network-planning)voor meer informatie over de netwerk vereisten voor de Hybrid Runbook Worker.

### <a name="enabling-servers-for-management-with-azure-automation-state-configuration"></a>Servers inschakelen voor beheer met Azure Automation status configuratie

Zie voor meer informatie over het inschakelen van servers voor beheer met Azure Automation status configuratie [machines inschakelen voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md).

Als Azure Automation [updatebeheer](automation-update-management.md) wordt ingeschakeld, wordt elke Windows-computer die is verbonden met uw log Analytics-werk ruimte automatisch geconfigureerd als een Hybrid Runbook worker ter ondersteuning van het bijwerken van het Runbook. Deze werk nemer is echter niet geregistreerd bij Hybrid Runbook Worker groepen die al zijn gedefinieerd in uw Automation-account.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>De computer toevoegen aan een Hybrid Runbook Worker groep

U kunt de werk computer toevoegen aan een Hybrid Runbook Worker groep in uw Automation-account. Houd er rekening mee dat u Automation-runbooks moet ondersteunen als u hetzelfde account gebruikt voor zowel de Azure Automation-functie als de Hybrid Runbook Worker groepslid maatschap. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van de Hybrid Runbook Worker.

## <a name="automated-deployment"></a>Geautomatiseerde implementatie

Voer op de doel computer de volgende stappen uit om de installatie en configuratie van de functie Windows Hybrid Worker te automatiseren.

### <a name="step-1---download-the-powershell-script"></a>Stap 1: het Power shell-script downloaden

Down load het script **New-OnPremiseHybridWorker. ps1** van de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). De down load moet rechtstreeks afkomstig zijn van de computer met de Hybrid Runbook Worker-functie of van een andere computer in uw omgeving. Wanneer u het script hebt gedownload, kopieert u het naar uw werk nemer. Het script **New-OnPremiseHybridWorker. ps1** maakt gebruik van de para meters die hieronder worden beschreven tijdens de uitvoering.

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
> Bij het inschakelen van functies ondersteunt Azure Automation alleen bepaalde regio's voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [regio toewijzing voor Automation-account en log Analytics-werk ruimte](how-to/region-mappings.md)voor een lijst met de ondersteunde toewijzings paren.

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Stap 2: Windows Power shell-opdracht regel shell openen

Open **Windows Power shell** vanuit het **Start** scherm in de beheerders modus.

### <a name="step-3---run-the-powershell-script"></a>Stap 3: het Power shell-script uitvoeren

Blader in de Power shell-opdracht regel shell naar de map die het script bevat dat u hebt gedownload. Wijzig de waarden voor de para meters,,,, `AutomationAccountName` `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` en `WorkspaceName` . Voer het script uit.

Nadat u het script hebt uitgevoerd, wordt u gevraagd om te verifiëren bij Azure. U moet zich aanmelden met een account dat lid is van de rol abonnements beheerders en mede beheerder van het abonnement.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Stap 4-NuGet installeren

U wordt gevraagd om akkoord te gaan met de installatie van NuGet en om te verifiëren met uw Azure-referenties. Als u niet de nieuwste versie van NuGet hebt, kunt u deze verkrijgen op basis van de [beschik bare NuGet-distributie versies](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Stap 5: de implementatie controleren

Nadat het script is voltooid, worden in de pagina Hybrid Worker groepen de nieuwe groep en het aantal leden weer gegeven. Als het een bestaande groep is, wordt het aantal leden verhoogd. U kunt de groep selecteren in de lijst op de pagina Hybrid Worker groepen en de tegel **Hybrid Workers** kiezen. Op de pagina Hybrid Workers kunt u elk lid van de groep weer geven.

## <a name="manual-deployment"></a>Handmatige implementatie

Voer de eerste twee stappen voor uw automatiserings omgeving uit op de doel computer. Voer vervolgens de resterende stappen voor elke werk computer uit.

### <a name="step-1---create-a-log-analytics-workspace"></a>Stap 1: een Log Analytics-werk ruimte maken

Als u nog geen Log Analytics-werk ruimte hebt, raadpleegt u de [richt lijnen voor het Azure monitor vastleggen](../azure-monitor/platform/design-logs-deployment.md) van het logboek voordat u de werk ruimte maakt.

### <a name="step-2---add-an-azure-automation-feature-to-the-log-analytics-workspace"></a>Stap 2: een Azure Automation-functie toevoegen aan de Log Analytics-werk ruimte

Een Automation-functie voegt functionaliteit toe voor Azure Automation, inclusief ondersteuning voor de Hybrid Runbook Worker. Wanneer u een Azure Automation-functie inschakelt in uw Log Analytics-werk ruimte, worden de werk onderdelen automatisch naar de agent computer gepusht.

Als u de functie Azure Automation, bijvoorbeeld Updatebeheer, wilt toevoegen aan uw werk ruimte, voert u de volgende Power shell-cmdlet uit:

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Stap 3: de Log Analytics-agent voor Windows installeren

De Log Analytics-agent voor Windows verbindt computers met een Azure Monitor Log Analytics-werk ruimte. Wanneer u de agent op de computer installeert en verbindt met uw werk ruimte, worden de onderdelen die vereist zijn voor de Hybrid Runbook Worker, automatisch gedownload.

Als u de agent op de computer wilt installeren, volgt u de instructies op [Windows-computers verbinden met Azure monitor-logboeken](../log-analytics/log-analytics-windows-agent.md). U kunt dit proces herhalen voor meerdere computers om meerdere werk nemers aan uw omgeving toe te voegen.

Wanneer de agent na een paar minuten verbinding heeft gemaakt met uw Log Analytics-werk ruimte, kunt u de volgende query uitvoeren om te controleren of er heartbeat-gegevens naar de werk ruimte worden verzonden.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

In de zoek resultaten ziet u heartbeat-records voor de computer, om aan te geven dat deze is verbonden en dat deze aan de service rapporteren. Standaard stuurt elke agent een heartbeat-record naar de toegewezen werk ruimte. Gebruik de volgende stappen om de installatie van de agent en de installatie te volt ooien.

1. Schakel de functie in om de agent computer toe te voegen. Zie [machines inschakelen in de werk ruimte](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Controleer of de agent de Azure Automation-functie correct heeft gedownload. 
3. Als u de versie van de Hybrid Runbook Worker wilt bevestigen, bladert u naar **C:\Program Files\Microsoft monitoring Agent\Agent\AzureAutomation** en noteert u de submap **Version** .

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Stap 4: de runbook-omgeving installeren en verbinding maken met Azure Automation

Wanneer u een agent configureert om te rapporteren aan een Log Analytics-werk ruimte, wordt door de Azure Automation `HybridRegistration` -functie de Power shell-module met de cmdlet gepusht `Add-HybridRunbookWorker` . Gebruik deze cmdlet om de runbook-omgeving op de computer te installeren en te registreren bij Azure Automation.

Open een Power shell-sessie in de beheerders modus en voer de volgende opdrachten uit om de module te importeren.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Voer nu de `Add-HybridRunbookWorker` cmdlet uit met de volgende syntaxis.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

U kunt de informatie die vereist is voor deze cmdlet, ophalen via de pagina sleutels beheren in de Azure Portal. Open deze pagina door **sleutels** te selecteren op de pagina instellingen in uw Automation-account.

![Pagina sleutels beheren](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Gebruik de `GroupName` naam van de Hybrid Runbook worker groep voor de para meter. Als deze groep al bestaat in het Automation-account, wordt de huidige computer hieraan toegevoegd. Als deze groep niet bestaat, wordt deze toegevoegd.
* Voor de `EndPoint` para meter gebruikt u het item **URL** op de pagina sleutels beheren.
* Voor de `Token` para meter gebruikt u de **primaire toegangs sleutel** vermelding op de pagina sleutels beheren.
* Stel, indien nodig, de `Verbose` para meter in om details over de installatie te ontvangen.

### <a name="step-5----install-powershell-modules"></a>Stap 5: Power shell-modules installeren

Runbooks kunnen gebruikmaken van de activiteiten en cmdlets die zijn gedefinieerd in de modules die in uw Azure Automation omgeving zijn geïnstalleerd. Omdat deze modules niet automatisch worden geïmplementeerd op on-premises computers, moet u ze hand matig installeren. De uitzonde ring hierop is de Azure-module. Deze module wordt standaard geïnstalleerd en biedt toegang tot cmdlets voor alle Azure-Services en-activiteiten voor Azure Automation.

Omdat het primaire doel van de Hybrid Runbook Worker is om lokale bronnen te beheren, moet u waarschijnlijk de modules installeren die deze bronnen ondersteunen, met name de `PowerShellGet` module. Zie [Windows Power shell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell)voor meer informatie over het installeren van Windows Power shell-modules.

Modules die zijn geïnstalleerd, moeten zich bevinden in een locatie waarnaar wordt verwezen door de `PSModulePath` omgevings variabele, zodat de Hybrid worker deze automatisch kan importeren. Zie [Installing modules in PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)(Engelstalig) voor meer informatie.

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-computer"></a><a name="remove-windows-hybrid-runbook-worker"></a>De Hybrid Runbook Worker verwijderen van een on-premises Windows-computer

1. Ga in het Azure Portal naar uw Automation-account.
2. Onder **account instellingen**selecteert u **sleutels** en noteert u de waarden voor **URL** en **primaire toegangs sleutel**.

3. Open een Power shell-sessie in de beheerders modus en voer de volgende opdracht uit met uw URL en primaire toegangs sleutel waarden. Gebruik de `Verbose` para meter voor een gedetailleerd logboek van het verwijderings proces. Als u verouderde machines uit uw Hybrid Worker groep wilt verwijderen, gebruikt u de optionele `machineName` para meter.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
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

* [Runbooks uitvoeren op een Hybrid Runbook Worker](automation-hrw-run-runbooks.md)
* [Problemen met Hybrid Runbook Worker oplossen](troubleshoot/hybrid-runbook-worker.md#windows)