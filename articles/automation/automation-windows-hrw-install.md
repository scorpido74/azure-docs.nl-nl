---
title: Hybrid Runbook Worker voor Azure Automation in Windows
description: Dit artikel bevat informatie over het installeren van een Azure Automation Hybrid Runbook Worker die u kunt gebruiken om runbooks uit te voeren op Windows-computers in uw lokale Data Center of in de cloud omgeving.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd599fcfe403d64483e6b4db869b93b26f5db754
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480820"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Een Windows-Hybrid Runbook Worker implementeren

U kunt de functie Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks uit te voeren op de computer waarop de rol wordt gehost en aan resources in de omgeving om deze lokale resources te beheren. Runbooks worden opgeslagen en beheerd in Azure Automation en vervolgens aan een of meer aangewezen computers geleverd. In dit artikel wordt beschreven hoe u de Hybrid Runbook Worker op een Windows-computer installeert.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>De Windows-Hybrid Runbook Worker installeren

Als u een Windows-Hybrid Runbook Worker wilt installeren en configureren, kunt u twee methoden gebruiken. De aanbevolen methode maakt gebruik van een Automation-runbook om het proces van het configureren van een Windows-computer volledig te automatiseren. De tweede methode volgt een stapsgewijze procedure voor het hand matig installeren en configureren van de rol.

> [!NOTE]
> Als u de configuratie wilt beheren van uw servers die ondersteuning bieden voor de Hybrid Runbook Worker rol met behulp van desired state Configuration (DSC), moet u deze toevoegen als DSC-knoop punten.

De minimale vereisten voor een Windows-Hybrid Runbook Worker zijn:

* Windows Server 2012 of hoger.
* Windows Power shell 5,1 of hoger ([down load WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET Framework 4.6.2 of hoger.
* Twee kernen.
* 4 GB RAM-geheugen.
* Poort 443 (uitgaand).

Zie [uw netwerk configureren](automation-hybrid-runbook-worker.md#network-planning)voor meer informatie over de netwerk vereisten voor de Hybrid Runbook Worker.

Zie voor meer informatie over het onboarden van servers voor beheer met DSC [onboarding-machines voor beheer door Azure Automation DSC](automation-dsc-onboarding.md).
Als u de [updatebeheer oplossing](../operations-management-suite/oms-solution-update-management.md)inschakelt, wordt elke Windows-computer die is verbonden met uw Azure log Analytics-werk ruimte automatisch geconfigureerd als een Hybrid Runbook worker voor de ondersteuning van runbooks die in deze oplossing zijn opgenomen. Het is echter niet geregistreerd bij Hybrid Worker groepen die al zijn gedefinieerd in uw Automation-account. 

De computer kan worden toegevoegd aan een Hybrid Runbook Worker groep in uw Automation-account ter ondersteuning van Automation-runbooks zolang u hetzelfde account gebruikt voor zowel de oplossing als het lidmaatschap van de Hybrid Runbook Worker-groep. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van de Hybrid Runbook Worker.

Nadat u een runbook worker hebt geïmplementeerd, raadpleegt u [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md) voor meer informatie over het configureren van runbooks voor het automatiseren van processen in uw on-premises Data Center of een andere cloud omgeving.

### <a name="automated-deployment"></a>Geautomatiseerde implementatie

Voer de volgende stappen uit om de installatie en configuratie van de functie Windows Hybrid Worker te automatiseren:

1. Down load het script New-OnPremiseHybridWorker. ps1 van de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) rechtstreeks van de computer waarop de Hybrid Runbook worker-functie of een andere computer in uw omgeving wordt uitgevoerd. Kopieer het script naar de werk nemer.

   Het script New-OnPremiseHybridWorker. ps1 vereist de volgende para meters tijdens de uitvoering:

   * *AutomationAccountName* (verplicht): de naam van uw Automation-account.
   * *AAResourceGroupName* (verplicht): de naam van de resource groep die is gekoppeld aan uw Automation-account.
   * *OMSResourceGroupName* (optioneel): de naam van de resource groep voor de log Analytics-werk ruimte. Als deze resource groep niet is opgegeven, wordt *AAResourceGroupName* gebruikt.
   * *HybridGroupName* (verplicht): de naam van een Hybrid Runbook worker groep die u opgeeft als doel voor de runbooks die dit scenario ondersteunen.
   * *SubscriptionID* (verplicht): de id van het Azure-abonnement waarin uw Automation-account zich bevindt.
   * *Workspacenaam* (optioneel): de naam van de log Analytics werk ruimte. Als u geen Log Analytics-werk ruimte hebt, maakt en configureert het script een.

   > [!NOTE]
   > Bij het inschakelen van oplossingen worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-Account.
   >
   > Zie [regio toewijzing voor Automation-account en log Analytics-werk ruimte](how-to/region-mappings.md)voor een lijst met de ondersteunde toewijzings paren.

2. Open **Windows Power shell** op uw computer in het **Start** scherm in de beheerders modus.
3. Blader vanuit de Power shell-opdracht regel shell naar de map die het script bevat dat u hebt gedownload. Wijzig de waarden voor de para meters *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId*en *-Workspace*. Voer het script uit.

     > [!NOTE]
     > Nadat u het script hebt uitgevoerd, wordt u gevraagd om te verifiëren bij Azure. U *moet* zich aanmelden met een account dat lid is van de rol abonnements beheerders en mede beheerder van het abonnement.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. U wordt gevraagd om akkoord te gaan met de installatie van NuGet en u wordt gevraagd om u te verifiëren met uw Azure-referenties.

5. Nadat het script is voltooid, worden in de pagina **Hybrid worker groepen** de nieuwe groep en het aantal leden weer gegeven. Als het een bestaande groep is, wordt het aantal leden verhoogd. U kunt de groep selecteren in de lijst op de pagina **Hybrid worker groepen** en de tegel **Hybrid Workers** selecteren. Op de pagina **Hybrid Workers** ziet u elk lid van de groep die wordt weer gegeven.

### <a name="manual-deployment"></a>Hand matige implementatie

Voer de eerste twee stappen voor uw automatiserings omgeving uit en herhaal de resterende stappen voor elke werk computer.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. een Log Analytics-werk ruimte maken

Als u nog geen Log Analytics-werk ruimte hebt, kunt u er een maken met behulp van de instructies in [uw werk ruimte beheren](../azure-monitor/platform/manage-access.md). U kunt een bestaande werk ruimte gebruiken als u er al een hebt.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. de automatiserings oplossing toevoegen aan de Log Analytics-werk ruimte

De oplossing Automation Azure Monitor logs voegt functionaliteit toe voor Azure Automation, inclusief ondersteuning voor Hybrid Runbook Worker. Wanneer u de oplossing aan uw werk ruimte toevoegt, worden de werk onderdelen automatisch gepusht naar de agent computer die u in de volgende stap gaat installeren.

Voer de volgende Power shell uit om de oplossing **automation** Azure monitor-Logboeken toe te voegen aan uw werk ruimte.

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. micro soft Monitoring Agent installeren

De micro soft Monitoring Agent verbindt computers met Azure Monitor-Logboeken. Wanneer u de agent op uw on-premises computer installeert en verbindt met uw werk ruimte, worden de onderdelen die vereist zijn voor de Hybrid Runbook Worker, automatisch gedownload.

Als u de agent op de on-premises computer wilt installeren, volgt u de instructies op [Windows-computers verbinden met Azure monitor-logboeken](../log-analytics/log-analytics-windows-agent.md). U kunt dit proces herhalen voor meerdere computers om meerdere werk nemers aan uw omgeving toe te voegen.

Wanneer de agent is verbonden met Azure Monitor logboeken, wordt deze weer gegeven op het tabblad **verbonden bronnen** van de pagina **instellingen** voor logboek analyse. U kunt controleren of de agent de automatiserings oplossing correct heeft gedownload wanneer deze een map bevat met de naam **AzureAutomationFiles** in C:\Program Files\Microsoft monitoring Agent\Agent. Als u de versie van de Hybrid Runbook Worker wilt controleren, gaat u naar C:\Program Files\Microsoft monitoring Agent\Agent\AzureAutomation\ en noteert u de submap \\*versie* .

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. de runbook-omgeving installeren en verbinding maken met Azure Automation

Wanneer u een agent toevoegt aan Azure Monitor logboeken, wordt de automatiserings oplossing gepusht naar de Power shell-module **HybridRegistration** , die de cmdlet **add-HybridRunbookWorker** bevat. U gebruikt deze cmdlet om de runbook-omgeving op de computer te installeren en te registreren bij Azure Automation.

Open een Power shell-sessie in de beheerders modus en voer de volgende opdrachten uit om de module te importeren:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Voer vervolgens de cmdlet **add-HybridRunbookWorker** uit met behulp van de volgende syntaxis:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

U kunt de informatie die vereist is voor deze cmdlet, ophalen via de pagina **sleutels beheren** in de Azure Portal. Open deze pagina door de optie **sleutels** te selecteren op de pagina **instellingen** van uw Automation-account.

![Pagina sleutels beheren](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** is de naam van de Hybrid Runbook worker groep. Als deze groep al bestaat in het Automation-account, wordt de huidige computer hieraan toegevoegd. Als deze groep niet bestaat, wordt deze toegevoegd.
* **Eind punt** is het **URL** -item op de pagina **sleutels beheren** .
* **Token** is de **primaire toegangs sleutel** vermelding op de pagina **sleutels beheren** .

Als u gedetailleerde informatie over de installatie wilt ontvangen, gebruikt u de switch **-verbose** met **add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Power shell-modules installeren

Runbooks kunnen gebruikmaken van de activiteiten en cmdlets die zijn gedefinieerd in de modules die in uw Azure Automation omgeving zijn geïnstalleerd. Deze modules worden niet automatisch geïmplementeerd op on-premises computers, dus u moet ze hand matig installeren. De uitzonde ring is de Azure-module, die standaard wordt geïnstalleerd en die toegang biedt tot cmdlets voor alle Azure-Services en-activiteiten voor Azure Automation.

Omdat het primaire doel van de functie voor Hybrid Runbook Worker het beheren van lokale resources is, moet u waarschijnlijk de modules installeren die deze resources ondersteunen. Zie [modules installeren](/powershell/scripting/developer/windows-powershell)voor meer informatie over het installeren van Windows Power shell-modules. 

Modules die zijn geïnstalleerd, moeten zich bevinden in een locatie waarnaar wordt verwezen door de omgevings variabele **PSModulePath** , zodat de Hybrid worker deze automatisch kan importeren. Zie [PSModulePath wijzigen](/powershell/scripting/developer/windows-powershell)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen te automatiseren in uw on-premises Data Center of andere cloud omgeving.
* Zie [Azure Automation Hybrid Runbook Workers verwijderen](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)voor instructies voor het verwijderen van Hybrid runbook Workers.
* Zie [problemen met Windows Hybrid Runbook Workers oplossen](troubleshoot/hybrid-runbook-worker.md#windows) voor meer informatie over het oplossen van problemen met uw Hybrid runbook Workers
* Zie [updatebeheer: problemen oplossen](troubleshoot/update-management.md)voor aanvullende stappen voor het oplossen van problemen met updatebeheer.
