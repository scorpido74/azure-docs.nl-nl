---
title: Hybrid Runbook Worker voor Azure Automation in Linux
description: In dit artikel vindt u informatie over het installeren van een Azure Automation Hybrid Runbook Worker, zodat u boeken uitvoeren op op Linux gebaseerde computers in uw lokale datacenter of cloudomgeving.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 2579748d9c68512e51fe46ec70084c30d06953bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278764"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Een Linux-hybride runbookworker implementeren

U de functie Hybride runbookworker van Azure Automation gebruiken om boeken rechtstreeks uit te voeren op de computer die de rol host en tegen resources in de omgeving om deze lokale bronnen te beheren. De Linux Hybrid Runbook Worker voert runbooks uit als een speciale gebruiker die kan worden verhoogd voor het uitvoeren van opdrachten die hoogte nodig hebben. Runbooks worden opgeslagen en beheerd in Azure Automation en vervolgens geleverd aan een of meer aangewezen computers.

In dit artikel wordt beschreven hoe u de Hybride Runbook Worker op een Linux-machine installeren.

## <a name="supported-linux-operating-systems"></a>Ondersteunde Linux-besturingssystemen

De functie Hybride runbookworker ondersteunt de volgende distributies:

* Amazon Linux 2012.09 t/m 2015.09 (x86/x64)
* CentOS Linux 5, 6 en 7 (x86/x64)
* Oracle Linux 5, 6 en 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 en 7 (x86/x64)
* Debian GNU/Linux 6, 7 en 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS en 18.04 (x86/x64)
* SUSE Linux Enterprise Server 11 en 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Een Linux-hybride runbookworker installeren

Als u een hybride runbookworker op uw Linux-computer wilt installeren en configureren, volgt u een eenvoudig proces om de rol handmatig te installeren en te configureren. Hiervoor moet de **oplossing Voor hybride werker van automatisering** in uw Azure Log Analytics-werkruimte worden inschakelt en moet u vervolgens een reeks opdrachten uitvoeren om de computer als werknemer te registreren en aan een groep toe te voegen.

De minimumvereisten voor een Linux Hybrid Runbook Worker zijn:

* Twee kernen
* 4 GB aan RAM-geheugen
* Poort 443 (uitgaand)

### <a name="package-requirements"></a>Pakketvereisten

| **Vereist pakket** | **Beschrijving** | **Minimale versie**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotheek| 2.5-12 |
|Openssl| OpenSSL-bibliotheken | 1.0 (TLS 1.1 en TLS 1.2 worden ondersteund|
|Curl | cURL-webclient | 7.15.5|
|Python-ctypes | Python 2.x is vereist |
|PAM | Pluggable Authentication Modules|
| **Optioneel pakket** | **Beschrijving** | **Minimale versie**|
| PowerShell Core | Om PowerShell-runbooks uit te voeren, moet PowerShell worden geïnstalleerd, zie [PowerShell Core installeren op Linux](/powershell/scripting/install/installing-powershell-core-on-linux) om te leren hoe u deze installeren.  | 6.0.0 |

### <a name="installation"></a>Installeren

Noteer voordat u verdergaat de werkruimte Log Analytics waaraan uw Automatiseringsaccount is gekoppeld. Let ook op de primaire sleutel voor uw Automatiseringsaccount. U beide vinden in de Azure-portal door uw automatiseringsaccount te selecteren, **Werkruimte** voor de werkruimte-id te selecteren en **Sleutels** voor de primaire sleutel te selecteren. Zie [Uw netwerk configureren](automation-hybrid-runbook-worker.md#network-planning)voor informatie over poorten en adressen die u nodig hebt voor de hybride runbookworker.

1. Schakel de oplossing **voor hybride werker voor automatisering** in Azure in met een van de volgende methoden:

   * Voeg de **oplossing voor hybride werker voor automatisering** toe aan uw abonnement met behulp van de procedure bij Azure [Monitor-logboeken toevoegen aan uw werkruimte.](../log-analytics/log-analytics-add-solutions.md)
   * Voer de volgende cmdlet uit:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installeer de agent Log Analytics voor Linux door de volgende opdracht uit te voeren. Vervang \<\> WorkspaceID \<en\> WorkspaceKey door de juiste waarden uit uw werkruimte.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Voer de volgende opdracht uit en wijzig de waarden voor de parameters *-w,* *-k,* *-g*en *-e*. Vervang voor de parameter *-g* de waarde door de naam van de groep Hybride Runbook Worker waarvan de nieuwe Linux Hybrid Runbook Worker lid moet worden. Als de naam niet bestaat in uw Automatiseringsaccount, wordt een nieuwe groep Hybride Runbook Worker gemaakt met die naam.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Nadat de opdracht is voltooid, toont de pagina **Hybride werknemersgroepen** in de Azure-portal de nieuwe groep en het aantal leden. Als dit een bestaande groep is, wordt het aantal leden verhoogd. U de groep selecteren in de lijst op de pagina **Hybride werknemersgroepen** en de tegel **Hybride werknemers** selecteren. Op de pagina **Hybride werknemers** ziet u elk lid van de groep dat wordt vermeld.

> [!NOTE]
> Als u de azure monitor-extensie voor virtuele machines voor `autoUpgradeMinorVersion` Linux gebruikt voor een Azure VM, raden we u aan false in te stellen, omdat versies voor automatisch upgraden problemen kunnen veroorzaken met de Hybride Runbook Worker. Zie [Azure CLI-implementatie ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)voor meer informatie over het handmatig upgraden van de extensie.

## <a name="turning-off-signature-validation"></a>Handtekeningvalidatie uitschakelen

Linux Hybrid Runbook Workers vereisen standaard handtekeningvalidatie. Als u een niet-ondertekend runbook uitvoert tegen een werknemer, ziet u een fout met de tekst 'Handtekeningvalidatie is mislukt'. Als u handtekeningvalidatie wilt uitschakelen, voert u de volgende opdracht uit. Vervang de tweede parameter door uw log analytics workspace ID.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Ondersteunde runbook-typen

Linux Hybrid Runbook Workers ondersteunen niet de volledige set runbook-typen in Azure Automation.

De volgende runbook-typen werken op een Linux-hybride werker:

* Python 2
* PowerShell

  > [!NOTE]
  > PowerShell-runbooks vereisen dat PowerShell Core op de Linux-machine wordt geïnstalleerd. Zie [PowerShell Core installeren op Linux](/powershell/scripting/install/installing-powershell-core-on-linux) voor meer informatie over het installeren ervan.

De volgende runbook-typen werken niet op een Linux-hybride werker:

* PowerShell-werkstroom
* Grafisch
* Grafische PowerShell-werkstroom

## <a name="next-steps"></a>Volgende stappen

* Zie Logboeken uitvoeren [op een hybride runbookworker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen in uw on-premises datacenter of andere cloudomgeving te automatiseren.
* Zie [Azure Automation Hybrid Runbook Workers verwijderen](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)voor instructies voor het verwijderen van hybride runbookworkers .
* Zie Problemen met Linux Hybrid Runbook Workers voor meer informatie over het oplossen van problemen met uw hybride [runbook-werknemers.](troubleshoot/hybrid-runbook-worker.md#linux)
