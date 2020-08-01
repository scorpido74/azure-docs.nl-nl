---
title: Een Linux-Hybrid Runbook Worker implementeren in Azure Automation
description: In dit artikel wordt uitgelegd hoe u een Azure Automation Hybrid Runbook Worker installeert voor het uitvoeren van runbooks op Linux-computers in uw lokale Data Center of cloud omgeving.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 7f19aec65ed2616d757718116ac948473dd4b0ed
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448009"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Een Linux-Hybrid Runbook Worker implementeren

U kunt de functie Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks uit te voeren op de computer waarop de rol wordt gehost en aan resources in de omgeving om deze lokale resources te beheren. De Linux-Hybrid Runbook Worker voert runbooks uit als een speciale gebruiker die kan worden uitgebreid voor het uitvoeren van opdrachten die moeten worden uitgebreid. Azure Automation runbooks opslaat en beheert en vervolgens aan een of meer aangewezen computers worden geleverd. In dit artikel wordt beschreven hoe u de Hybrid Runbook Worker op een Linux-computer installeert, hoe u de werk nemer verwijdert en hoe u een Hybrid Runbook Worker groep verwijdert.

Nadat u een runbook worker hebt geïmplementeerd, raadpleegt u [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md) voor meer informatie over het configureren van runbooks voor het automatiseren van processen in uw on-premises Data Center of een andere cloud omgeving.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u over het volgende beschikt:

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

De Hybrid Runbook Worker-rol vereist de [log Analytics-agent](../azure-monitor/platform/log-analytics-agent.md) voor het ondersteunde Linux-besturings systeem.

### <a name="supported-linux-operating-systems"></a>Ondersteunde Linux-besturingssystemen

De functie Hybrid Runbook Worker ondersteunt de volgende distributies:

* Amazon Linux 2012,09 tot 2015,09 (x86/x64)
* CentOS Linux 5, 6 en 7 (x86/x64)
* Oracle Linux 5, 6 en 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 en 7 (x86/x64)
* Debian GNU/Linux 6, 7 en 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS en 18,04 (x86/x64)
* SUSE Linux Enterprise Server 11 en 12 (x86/x64)

### <a name="minimum-requirements"></a>Minimale vereisten

De minimale vereisten voor een Linux-Hybrid Runbook Worker zijn:

* Twee kernen
* 4 GB aan RAM-geheugen
* Poort 443 (uitgaand)

| **Vereist pakket** | **Beschrijving** | **Minimale versie**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotheek| 2.5-12 |
|Openssl| OpenSSL-bibliotheken | 1,0 (TLS 1,1 en TLS 1,2 worden ondersteund)|
|Curl | Krul webclient | 7.15.5|
|Python-ctypes | Python 2. x is vereist |
|PAM | Pluggable Authentication Modules|
| **Optioneel pakket** | **Beschrijving** | **Minimale versie**|
| PowerShell Core | Als u Power shell-runbooks wilt uitvoeren, moet Power shell Core zijn geïnstalleerd. Zie [Power shell core in Linux installeren](/powershell/scripting/install/installing-powershell-core-on-linux) voor meer informatie over het installeren ervan. | 6.0.0 |

## <a name="supported-runbook-types"></a>Ondersteunde typen runbook

Hybrid Runbook Workers van Linux ondersteunen een beperkt aantal typen Runbook in Azure Automation en ze worden beschreven in de volgende tabel.

|Type Runbook | Ondersteund |
|-------------|-----------|
|Python 2 |Ja |
|PowerShell |Ja<sup>1</sup> |
|PowerShell-werkstroom |Nee |
|Grafisch |Nee |
|Grafische power shell-werk stroom |Nee |

<sup>1</sup> Power shell-runbooks vereisen dat Power shell core wordt geïnstalleerd op de Linux-machine. Zie [Power shell core in Linux installeren](/powershell/scripting/install/installing-powershell-core-on-linux) voor meer informatie over het installeren ervan.

## <a name="install-a-linux-hybrid-runbook-worker"></a>Een Linux-Hybrid Runbook Worker installeren

Als u een Linux-Hybrid Runbook Worker wilt installeren en configureren, voert u de volgende stappen uit.

1. Implementeer de Log Analytics-agent op de doel computer.

    * Voor Azure-Vm's installeert u de Log Analytics-agent voor Linux met behulp [van de virtuele-machine extensie voor Linux](../virtual-machines/extensions/oms-linux.md). Met de uitbrei ding wordt de Log Analytics agent geïnstalleerd op virtuele machines van Azure en worden virtuele machines geregistreerd in een bestaande Log Analytics-werk ruimte met behulp van een Azure Resource Manager-sjabloon of de Azure CLI. Zodra de agent is geïnstalleerd, kan de virtuele machine worden toegevoegd aan een Hybrid Runbook Worker groep in uw Automation-account.

    * Voor niet-Azure Vm's installeert u de Log Analytics-agent voor Linux met behulp van de implementatie opties die worden beschreven in het artikel [verbinding maken met Linux-computers met Azure monitor](../azure-monitor/platform/agent-linux.md) . U kunt dit proces herhalen voor meerdere machines om meerdere werk nemers aan uw omgeving toe te voegen. Zodra de agent is geïnstalleerd, kunnen de virtuele machines worden toegevoegd aan een Hybrid Runbook Worker groep in uw Automation-account.

    > [!NOTE]
    > Als u de configuratie wilt beheren van machines die ondersteuning bieden voor de Hybrid Runbook Worker rol met behulp van desired state Configuration (DSC), moet u de computers als DSC-knoop punten toevoegen.

    > [!NOTE]
    > Het [nxautomation-account](automation-runbook-execution.md#log-analytics-agent-for-linux) met de bijbehorende sudo-machtigingen moet aanwezig zijn tijdens de installatie van de Linux-Hybrid Worker. Als u de werk nemer probeert te installeren en het account niet aanwezig is of niet de juiste machtigingen heeft, mislukt de installatie.

2. Controleer of de agent wordt gerapporteerd aan de werk ruimte.

    De Log Analytics-agent voor Linux verbindt machines met een Azure Monitor Log Analytics-werk ruimte. Wanneer u de agent op de computer installeert en verbindt met uw werk ruimte, worden automatisch de onderdelen gedownload die vereist zijn voor de Hybrid Runbook Worker.

    Wanneer de agent na een paar minuten verbinding heeft gemaakt met uw Log Analytics-werk ruimte, kunt u de volgende query uitvoeren om te controleren of er heartbeat-gegevens naar de werk ruimte worden verzonden.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    In de zoek resultaten ziet u heartbeat-records voor de machine, waarmee wordt aangegeven dat deze is verbonden en dat er wordt gerapporteerd aan de service. Standaard stuurt elke agent een heartbeat-record naar de toegewezen werk ruimte.

3. Voer de volgende opdracht uit om de machine toe te voegen aan een Hybrid Runbook worker groep, waarbij u de waarden opgeeft voor de para meters,, `-w` `-k` `-g` en `-e` .

    U kunt de vereiste gegevens voor para meters `-k` en `-e` op de pagina **sleutels** in uw Automation-account ophalen. Selecteer **sleutels** onder de sectie **account instellingen** aan de linkerkant van de pagina.

    ![Pagina sleutels beheren](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * `-e`Kopieer de waarde voor **URL**voor de para meter.

    * Kopieer voor de `-k` para meter de waarde voor de **primaire toegangs sleutel**.

    * Geef voor de `-g` para meter de naam op van de Hybrid Runbook worker groep waaraan de nieuwe Linux Hybrid Runbook worker moet worden toegevoegd. Als deze groep al bestaat in het Automation-account, wordt de huidige machine hieraan toegevoegd. Als deze groep niet bestaat, wordt deze met die naam gemaakt.

    * Geef voor de `-w` para meter uw log Analytics werk ruimte-id op.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

4. Nadat de opdracht is voltooid, toont de pagina Hybrid Worker groepen in uw Automation-account de nieuwe groep en het aantal leden. Als dit een bestaande groep is, wordt het aantal leden verhoogd. U kunt de groep selecteren in de lijst op de pagina Hybrid Worker groepen en de tegel **Hybrid Workers** selecteren. Op de pagina Hybrid Workers ziet u elk lid van de groep die wordt weer gegeven.

    > [!NOTE]
    > Als u de Log Analytics extensie van de virtuele machine voor Linux voor een Azure-VM gebruikt, wordt u aangeraden om in te stellen dat er `autoUpgradeMinorVersion` `false` problemen met de Hybrid Runbook Worker kunnen ontstaan door de versie van automatische upgrades. Zie [Azure cli-implementatie](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)voor meer informatie over het hand matig bijwerken van de extensie.

## <a name="turn-off-signature-validation"></a>Handtekening validatie uitschakelen

Voor Linux Hybrid Runbook Workers is standaard handtekening validatie vereist. Als u een niet-ondertekend runbook uitvoert op een werk nemer, ziet u een `Signature validation failed` fout melding. Voer de volgende opdracht uit om handtekening validatie uit te scha kelen. Vervang de tweede para meter door uw Log Analytics werk ruimte-ID.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-machine"></a><a name="remove-linux-hybrid-runbook-worker"></a>De Hybrid Runbook Worker van een on-premises Linux-machine verwijderen

U kunt de opdracht `ls /var/opt/microsoft/omsagent` op de Hybrid Runbook worker gebruiken om de werk ruimte-id op te halen. Er wordt een map gemaakt met de naam van de werk ruimte-ID.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Met dit script wordt de Log Analytics-agent voor Linux niet van de computer verwijderd. De functie en configuratie van de Hybrid Runbook Worker rol worden alleen verwijderd.

## <a name="remove-a-hybrid-worker-group"></a>Een Hybrid Worker-groep verwijderen

Als u een Hybrid Runbook Worker groep van Linux-machines wilt verwijderen, gebruikt u dezelfde stappen als voor een Windows Hybrid worker-groep. Zie [een Hybrid worker groep verwijderen](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen te automatiseren in uw on-premises Data Center of andere cloud omgeving.

* Zie [problemen met Hybrid Runbook worker problemen oplossen-Linux](troubleshoot/hybrid-runbook-worker.md#linux)voor meer informatie over het oplossen van problemen met uw Hybrid Runbook Workers.
