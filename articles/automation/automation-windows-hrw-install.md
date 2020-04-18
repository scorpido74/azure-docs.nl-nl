---
title: Hybrid Runbook Worker voor Azure Automation in Windows
description: In dit artikel vindt u informatie over het installeren van een Azure Automation Hybrid Runbook Worker die u gebruiken om boeken uit te voeren op Windows-computers in uw lokale datacenter of cloudomgeving.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 53dfe07ebd4925c96290db140b6e613c38eef564
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617343"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Een Windows Hybrid Runbook Worker implementeren

U de functie Hybride runbookworker van Azure Automation gebruiken om boeken rechtstreeks uit te voeren op de computer die de rol host en tegen resources in de omgeving om deze lokale bronnen te beheren. Azure Automation slaat runbooks op en beheert deze vervolgens op een of meer aangewezen computers. In dit artikel wordt beschreven hoe u een hybride runbookworker implementeert op een Windows-machine.

Nadat u een runbook-werknemer hebt geïmplementeerd, bekijkt u [Runbooks op een hybride runbookwerker](automation-hrw-run-runbooks.md) om te leren hoe u uw runbooks configureert om processen in uw on-premises datacenter of andere cloudomgeving te automatiseren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Installatie en configuratie van Windows Hybrid Runbook Worker

Als u een Windows Hybrid Runbook Worker wilt installeren en configureren, u een van de volgende methoden gebruiken.

* Installeer voor Azure VM's de loganalyse-agent voor Windows met behulp van de [extensie voor virtuele machines voor Windows.](../virtual-machines/extensions/oms-windows.md) De extensie installeert de Log Analytics-agent op virtuele Azure-machines en schrijft virtuele machines in een bestaande Log Analytics-werkruimte met behulp van een Azure Resource Manager-sjabloon of PowerShell. Zodra de agent is geïnstalleerd, kan de VM worden toegevoegd aan een hybride runbookworkergroep in uw automatiseringsaccount. Raadpleeg stap 3 en 4 in de sectie [Handmatige implementatie.](#manual-deployment)

* Gebruik een automation-runbook om het configureren van een Windows-computer volledig te automatiseren. Dit is de aanbevolen methode voor machines in uw datacenter of een andere cloudomgeving.

* Volg een stapsgewijze procedure om de rol Hybride runbookworker handmatig te installeren en configureren op uw niet-Azure VM.

> [!NOTE]
> Als u de configuratie wilt beheren van servers die de rol Hybride Runbook Worker met gewenste statusconfiguratie (DSC) ondersteunen, moet u de servers als DSC-knooppunten toevoegen.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Minimumvereisten voor Windows Hybrid Runbook Worker

De minimumvereisten voor een Windows Hybrid Runbook Worker zijn:

* Windows Server 2012 of hoger
* Windows PowerShell 5.1 of hoger[(WMF 5.1 downloaden)](https://www.microsoft.com/download/details.aspx?id=54616)
* .NET framework 4.6.2 of hoger
* Twee kernen
* 4 GB aan RAM-geheugen
* Poort 443 (uitgaand)

### <a name="network-configuration"></a>Netwerkconfiguratie

Zie [Uw netwerk configureren](automation-hybrid-runbook-worker.md#network-planning)voor meer netwerkvereisten voor de hybride runbookworker.

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Server onboarding voor beheer met Automation DSC

Zie [Onboarding-machines voor beheer door Azure Automation DSC voor](automation-dsc-onboarding.md)informatie over onboarding-servers voor beheer bij DSC.

Als u de [oplossing Updatebeheer](../operations-management-suite/oms-solution-update-management.md) inschakelt, configureert u automatisch elke Windows-computer die is verbonden met uw Log Analytics-werkruimte als hybride runbookworker om runbooks in de oplossing te ondersteunen. Deze werknemer is echter niet geregistreerd bij hybride runbookworkergroepen die al zijn gedefinieerd in uw Automatiseringsaccount.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Toevoeging van de computer aan een hybride runbookworkergroep

U de werknemerscomputer toevoegen aan een groep hybride runbookworker in uw automatiseringsaccount. Houd er rekening mee dat u automatiseringsrunbooks moet ondersteunen zolang u hetzelfde account gebruikt voor zowel de oplossing als het lidmaatschap van de hybride runbookworkergroep. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van de Hybrid Runbook Worker.

## <a name="automated-deployment"></a>Geautomatiseerde implementatie

Voer op de doelmachine de volgende stappen uit om de installatie en configuratie van de rol Windows Hybrid Worker te automatiseren.

### <a name="step-1---download-the-powershell-script"></a>Stap 1 - Het PowerShell-script downloaden

Download het script **New-OnPremiseHybridWorker.ps1** uit de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). De download moet rechtstreeks van de computer met de rol Hybride Runbook Worker of vanaf een andere computer in uw omgeving zijn. Wanneer u het script hebt gedownload, kopieert u het naar uw werknemer. Het **script New-OnPremiseHybridWorker.ps1** gebruikt de onderstaande parameters tijdens de uitvoering.

| Parameter | Status | Beschrijving |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Verplicht | De naam van de resourcegroep die is gekoppeld aan uw Automatiseringsaccount. |
| `AutomationAccountName` | Verplicht | De naam van uw Automation-account.
| `Credential` | Optioneel | De referenties die u moet gebruiken bij het inloggen op de Azure-omgeving. |
| `HybridGroupName` | Verplicht | De naam van een hybride runbookworkergroep die u opgeeft als doel voor de runbooks die dit scenario ondersteunen. |
| `OMSResourceGroupName` | Optioneel | De naam van de resourcegroep voor de werkruimte Log Analytics. Als deze resourcegroep niet is `AAResourceGroupName` opgegeven, wordt de waarde van de groep gebruikt. |
| `SubscriptionID` | Verplicht | De id van het Azure-abonnement dat is gekoppeld aan uw Automatiseringsaccount. |
| `TenantID` | Optioneel | De id van de tenantorganisatie die is gekoppeld aan uw Automatiseringsaccount. |
| `WorkspaceName` | Optioneel | De naam van de Log Analytics-werkruimte. Als u geen Log Analytics-werkruimte hebt, maakt en configureert het script er een. |

> [!NOTE]
> Bij het inschakelen van oplossingen ondersteunt Azure Automation alleen bepaalde regio's voor het koppelen van een Log Analytics-werkruimte en een Automatiseringsaccount. Zie [Regiotoewijzing voor Automatiseringsaccount en Log Analytics-werkruimte voor](how-to/region-mappings.md)een lijst met ondersteunde toewijzingsparen.

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Stap 2 - De opdrachtregelshell van Windows PowerShell openen

Open **Windows PowerShell** vanaf **het** startscherm in de beheerdersmodus.

### <a name="step-3---run-the-powershell-script"></a>Stap 3 - Het PowerShell-script uitvoeren

Blader in de opdrachtregelshell PowerShell naar de map met het script dat u hebt gedownload. Wijzig de waarden `AutomationAccountName`voor `AAResourceGroupName` `OMSResourceGroupName`de `HybridGroupName` `SubscriptionID`parameters `WorkspaceName`, , , en . Voer dan het script uit.

U wordt gevraagd om te verifiëren met Azure nadat u het script hebt uitgevoerd. U moet zich aanmelden met een account dat lid is van de rol Abonnementsbeheerders en medebeheerder van het abonnement.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Stap 4 - NuGet installeren

U wordt gevraagd ermee in te stemmen NuGet te installeren en te verifiëren met uw Azure-referenties. Als je niet de nieuwste NuGet-versie hebt, kun je deze verkrijgen van [beschikbare NuGet-distributieversies.](https://www.nuget.org/downloads)

### <a name="step-5---verify-the-deployment"></a>Stap 5 - De implementatie verifiëren

Nadat het script is voltooid, toont de pagina Hybride werknemersgroepen de nieuwe groep en het aantal leden. Als het een bestaande groep is, wordt het aantal leden verhoogd. U de groep selecteren in de lijst op de pagina Hybride werknemersgroepen en de tegel **Hybride werknemers** kiezen. Op de pagina Hybride werknemers ziet u elk lid van de groep dat wordt vermeld.

## <a name="manual-deployment"></a>Handmatige implementatie

Voer op de doelmachine de eerste twee stappen één keer uit voor uw automatiseringsomgeving. Voer vervolgens de resterende stappen uit voor elke werknemercomputer.

### <a name="step-1---create-a-log-analytics-workspace"></a>Stap 1 - Een werkruimte voor Logboekanalyse maken

Als u nog geen werkruimte voor Log Analytics hebt, controleert u de [ontwerprichtlijnen voor Azure Monitor Log](../azure-monitor/platform/design-logs-deployment.md) voordat u de werkruimte maakt.

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>Stap 2 - De automatiseringsoplossing toevoegen aan de werkruimte Log Analytics

De automatiseringsoplossing voegt functionaliteit toe voor Azure Automation, inclusief ondersteuning voor de Hybride Runbook Worker. Wanneer u de oplossing toevoegt aan uw Log Analytics-werkruimte, wordt automatisch naar de agentcomputer de werknemerscomponenten gepusht die u installeert zoals beschreven in de volgende stap.

Voer de volgende PowerShell-cmdlet uit om de automatiseringsoplossing aan uw werkruimte toe te voegen.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Stap 3 - De log-analyse-agent voor Windows installeren

De agent Log Analytics voor Windows verbindt computers met een Azure Monitor Log Analytics-werkruimte. Wanneer u de agent op uw computer installeert en deze aansluit op uw werkruimte, worden automatisch de onderdelen gedownload die nodig zijn voor de hybride runbookworker.

Als u de agent op de computer wilt installeren, volgt u de instructies bij [Windows-computers verbinden met Azure Monitor-logboeken.](../log-analytics/log-analytics-windows-agent.md) U dit proces herhalen voor meerdere computers om meerdere werknemers aan uw omgeving toe te voegen.

Wanneer de agent na enkele minuten verbinding heeft gemaakt met uw Log Analytics-werkruimte, u de volgende query uitvoeren om te controleren of de agent heartbeatgegevens naar de werkruimte verzendt.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

In de zoekresultaten ziet u hartslagrecords voor de computer, wat aangeeft dat deze is verbonden en rapporteert aan de service. Standaard stuurt elke agent een heartbeatrecord door naar de toegewezen werkruimte. 

Gebruik de volgende stappen om de installatie en installatie van de agent uit te voeren.

1. Schakel de oplossing in om aan boord van de agentmachine te gaan. Zie [Boordmachines in de werkruimte](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Controleer of de agent de automatiseringsoplossing correct heeft gedownload. 
3. Als u de versie van de hybride runbookworker wilt bevestigen, bladert u naar **C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation** en noteer de **submap van** de versie.

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Stap 4 - Installeer de runbook-omgeving en maak verbinding met Azure Automation

Wanneer u een agent configureert om te rapporteren aan `HybridRegistration` een Log Analytics-werkruimte, wordt met de automatiseringsoplossing de PowerShell-module, die de `Add-HybridRunbookWorker` cmdlet bevat, omlaag gepusht. Gebruik deze cmdlet om de runbook-omgeving op de computer te installeren en te registreren bij Azure Automation.

Open een PowerShell-sessie in de administratormodus en voer de volgende opdrachten uit om de module te importeren.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Voer nu `Add-HybridRunbookWorker` de cmdlet uit met de volgende syntaxis.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

U de informatie die nodig is voor deze cmdlet ophalen via de pagina Sleutels beheren in de Azure-portal. Open deze pagina door **Toetsen te** selecteren op de pagina Instellingen in uw automatiseringsaccount.

![Pagina Sleutels beheren](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Gebruik `GroupName` voor de parameter de naam van de groep Hybride runbookworker. Als deze groep al bestaat in het account Automatisering, wordt de huidige computer eraan toegevoegd. Als deze groep niet bestaat, wordt deze toegevoegd.
* Gebruik `EndPoint` voor de parameter de **URL-vermelding** op de pagina Sleutels beheren.
* Gebruik `Token` voor de parameter het item **PRIMAIRE TOEGANGSSLEUTEL** op de pagina Sleutels beheren.
* Stel indien nodig `Verbose` de parameter in om details over de installatie te ontvangen.

### <a name="step-5----install-powershell-modules"></a>Stap 5 - PowerShell-modules installeren

Runbooks kunnen gebruik maken van alle activiteiten en cmdlets die zijn gedefinieerd in de modules die zijn geïnstalleerd in uw Azure Automation-omgeving. Aangezien deze modules niet automatisch worden geïmplementeerd op on-premises computers, moet u ze handmatig installeren. De uitzondering is de Azure-module. Deze module is standaard geïnstalleerd en biedt toegang tot cmdlets voor alle Azure-services en -activiteiten voor Azure Automation.

Omdat het primaire doel van de functie Hybride Runbook Worker is om lokale bronnen te beheren, moet u waarschijnlijk de modules installeren die deze bronnen ondersteunen, met name de `PowerShellGet` module. Zie [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell)voor informatie over het installeren van Windows PowerShell-modules.

Modules die zijn geïnstalleerd, moeten zich `PSModulePath` bevinden op een locatie waarnaar wordt verwezen door de omgevingsvariabele, zodat de hybride werknemer ze automatisch kan importeren. Zie [Modules installeren in PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie Logboeken uitvoeren [op een hybride runbookworker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen in uw on-premises datacenter of andere cloudomgeving te automatiseren.
* Zie Azure Automation Hybrid Runbook Workers verwijderen voor instructies voor het verwijderen van hybride [runbookworkers](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Zie Problemen met Windows Hybrid Runbook Workers voor meer informatie over het oplossen van problemen met uw hybride [runbookworkers.](troubleshoot/hybrid-runbook-worker.md#windows)
* Zie [Updatebeheer: probleemoplossing](troubleshoot/update-management.md)voor aanvullende stappen voor problemen met het oplossen van updates.
