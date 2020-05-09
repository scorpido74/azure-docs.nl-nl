---
title: Hybrid Runbook Worker voor Azure Automation in Linux
description: Dit artikel bevat informatie over het installeren van een Azure Automation Hybrid Runbook Worker, zodat u runbooks kunt uitvoeren op Linux-computers in uw lokale Data Center of in de cloud omgeving.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 82f6d9e56e5d5745077ef512cb3392c16b95961f
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872178"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Een Linux-Hybrid Runbook Worker implementeren

U kunt de functie Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks uit te voeren op de computer waarop de rol wordt gehost en aan resources in de omgeving om deze lokale resources te beheren. De Linux-Hybrid Runbook Worker voert runbooks uit als een speciale gebruiker die kan worden uitgebreid voor het uitvoeren van opdrachten die moeten worden uitgebreid. Runbooks worden opgeslagen en beheerd in Azure Automation en vervolgens aan een of meer aangewezen computers geleverd.

In dit artikel wordt beschreven hoe u de Hybrid Runbook Worker op een Linux-computer installeert, hoe u de werk nemer verwijdert en hoe u een Hybrid Runbook Worker groep verwijdert.

## <a name="supported-linux-operating-systems"></a>Ondersteunde Linux-besturingssystemen

De functie Hybrid Runbook Worker ondersteunt de volgende distributies:

* Amazon Linux 2012,09 tot 2015,09 (x86/x64)
* CentOS Linux 5, 6 en 7 (x86/x64)
* Oracle Linux 5, 6 en 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 en 7 (x86/x64)
* Debian GNU/Linux 6, 7 en 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS en 18,04 (x86/x64)
* SUSE Linux Enterprise Server 11 en 12 (x86/x64)

## <a name="supported-runbook-types"></a>Ondersteunde typen runbook

Linux Hybrid Runbook Workers bieden geen ondersteuning voor de volledige set Runbook-typen in Azure Automation.

De volgende typen runbook werken op een Linux-Hybrid Worker:

* Python 2
* PowerShell

  > [!NOTE]
  > Power shell-runbooks vereisen dat Power shell core wordt geïnstalleerd op de Linux-machine. Zie [Power shell core in Linux installeren](/powershell/scripting/install/installing-powershell-core-on-linux) voor meer informatie over het installeren ervan.

De volgende typen runbook werken niet op een Linux-Hybrid Worker:

* PowerShell-werkstroom
* Grafisch
* Grafische power shell-werk stroom

## <a name="deployment-requirements"></a>Implementatievereisten

De minimale vereisten voor een Linux-Hybrid Runbook Worker zijn:

* Twee kernen
* 4 GB aan RAM-geheugen
* Poort 443 (uitgaand)

### <a name="package-requirements"></a>Pakket vereisten

| **Vereist pakket** | **Beschrijving** | **Minimale versie**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotheek| 2.5-12 |
|Openssl| OpenSSL-bibliotheken | 1,0 (TLS 1,1 en TLS 1,2 worden ondersteund)|
|Curl | Krul webclient | 7.15.5|
|Python-ctypes | Python 2. x is vereist |
|PAM | Pluggable Authentication Modules|
| **Optioneel pakket** | **Beschrijving** | **Minimale versie**|
| PowerShell Core | Als u Power shell-runbooks wilt uitvoeren, moet Power shell zijn geïnstalleerd. Zie [Power shell Core installeren in Linux](/powershell/scripting/install/installing-powershell-core-on-linux) voor meer informatie over het installeren ervan.  | 6.0.0 |

## <a name="install-a-linux-hybrid-runbook-worker"></a>Een Linux-Hybrid Runbook Worker installeren

Als u een Hybrid Runbook Worker op uw Linux-computer wilt installeren en configureren, volgt u een eenvoudig hand matig proces. Hiervoor moet u de Automatisering-Hybrid Worker oplossing inschakelen in uw Azure Log Analytics-werk ruimte en vervolgens een reeks opdrachten uitvoeren om de computer als een werk nemer te registreren en toe te voegen aan een groep.

Voordat u verder gaat, moet u de Log Analytics-werk ruimte zien waaraan uw Automation-account is gekoppeld. Let ook op de primaire sleutel voor uw Automation-account. U kunt beide van de Azure Portal vinden door uw Automation-account te selecteren, **werk ruimte** te selecteren voor de werk ruimte-id en **sleutels** te selecteren voor de primaire sleutel. Zie [uw netwerk configureren](automation-hybrid-runbook-worker.md#network-planning)voor meer informatie over de poorten en adressen die u nodig hebt voor de Hybrid Runbook Worker.

>[!NOTE]
> Het [nxautomation-account](automation-runbook-execution.md#log-analytics-agent-for-linux) met de bijbehorende sudo-machtigingen moet aanwezig zijn tijdens de installatie van de Linux-Hybrid Worker. Als u de werk nemer probeert te installeren en het account niet aanwezig is of niet de juiste machtigingen heeft, mislukt de installatie.

1. Gebruik een van de volgende methoden om de Automatisering-Hybrid Worker-oplossing in Azure in te scha kelen:

   * Voeg de Automatisering-Hybrid Worker oplossing toe aan uw abonnement met behulp van de procedure in [Azure monitor logboek oplossingen toevoegen aan uw werk ruimte](../log-analytics/log-analytics-add-solutions.md).
   * Voer de volgende cmdlet uit:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installeer de Log Analytics-agent voor Linux door de volgende opdracht uit te voeren. Vervang \<WorkspaceID\> en \<WorkspaceKey\> door de juiste waarden uit uw werk ruimte.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Voer de volgende opdracht uit, waarbij u de waarden voor de para meters *-w*, *-k*, *-g*en *-e*wijzigt. Voor de para meter *-g* , vervang de waarde door de naam van de Hybrid Runbook worker groep waaraan de nieuwe Linux-Hybrid Runbook worker moet worden toegevoegd. Als de naam niet bestaat in uw Automation-account, wordt er een nieuwe Hybrid Runbook Worker groep met die naam gemaakt.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Nadat de opdracht is voltooid, worden in de pagina Hybrid Worker groepen in het Azure Portal de nieuwe groep en het aantal leden weer gegeven. Als dit een bestaande groep is, wordt het aantal leden verhoogd. U kunt de groep selecteren in de lijst op de pagina Hybrid Worker groepen en de tegel **Hybrid Workers** selecteren. Op de pagina Hybrid Workers ziet u elk lid van de groep die wordt weer gegeven.

> [!NOTE]
> Als u de Azure Monitor extensie van de virtuele machine voor Linux gebruikt voor een Azure VM, wordt `autoUpgradeMinorVersion` u aangeraden om in te stellen op False als de versies van automatische upgrades de Hybrid Runbook Worker kunnen veroorzaken. Zie [Azure cli-implementatie](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)voor meer informatie over het hand matig bijwerken van de extensie.

## <a name="turn-off-signature-validation"></a>Handtekening validatie uitschakelen

Voor Linux Hybrid Runbook Workers is standaard handtekening validatie vereist. Als u een niet-ondertekend runbook uitvoert op een werk nemer, ziet u `Signature validation failed` een fout melding. Voer de volgende opdracht uit om handtekening validatie uit te scha kelen. Vervang de tweede para meter door uw Log Analytics werk ruimte-ID.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-computer"></a><a name="remove-linux-hybrid-runbook-worker"></a>De Hybrid Runbook Worker verwijderen van een on-premises Linux-computer

U kunt de opdracht `ls /var/opt/microsoft/omsagent` op de Hybrid Runbook worker gebruiken om de werk ruimte-id op te halen. Er wordt een map gemaakt met de naam van de werk ruimte-ID.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Met deze code wordt de Log Analytics-agent voor Linux niet van de computer verwijderd. De functie en configuratie van de Hybrid Runbook Worker rol worden alleen verwijderd.

## <a name="remove-a-hybrid-worker-group"></a>Een Hybrid Worker-groep verwijderen

Als u een Hybrid Runbook Worker groep van Linux-computers wilt verwijderen, gebruikt u dezelfde stappen als voor een Windows Hybrid worker-groep. Zie [een Hybrid worker groep verwijderen](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen te automatiseren in uw on-premises Data Center of andere cloud omgeving.
* Zie [problemen met Linux Hybrid Runbook Workers oplossen](troubleshoot/hybrid-runbook-worker.md#linux) voor meer informatie over het oplossen van problemen met uw Hybrid runbook Workers
