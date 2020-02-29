---
title: Problemen oplossen-Azure Automation Hybrid Runbook Workers
description: In dit artikel vindt u informatie over het oplossen van problemen Azure Automation Hybrid Runbook Workers
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 137623e4c52d24061aec8ec11fca0fc02ca54c7f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190973"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Problemen met Hybrid Runbook Workers oplossen

Dit artikel bevat informatie over het oplossen van problemen met Hybrid Runbook Workers.

## <a name="general"></a>Algemeen

De Hybrid Runbook Worker is afhankelijk van een agent om te communiceren met uw Automation-account om de werk nemer te registreren, Runbook-taken te ontvangen en de status van het rapport. Voor Windows is deze agent de Log Analytics-agent voor Windows, ook wel micro soft Monitoring Agent (MMA) genoemd. Voor Linux is het de Log Analytics-agent voor Linux.

### <a name="runbook-execution-fails"></a>Scenario: het uitvoeren van een Runbook mislukt

#### <a name="issue"></a>Probleem

De Runbook-uitvoering is mislukt en de volgende fout wordt weer gegeven.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Het runbook is onderbroken kort nadat het drie keer is geprobeerd uit te voeren. Er zijn voor waarden die het runbook kunnen onderbreken van volt ooien. Het gerelateerde fout bericht bevat mogelijk geen aanvullende informatie.

#### <a name="cause"></a>Oorzaak

Mogelijke oorzaken zijn:

* De runbooks kunnen niet worden geverifieerd met lokale resources.

* De Hybrid worker bevindt zich achter een proxy of firewall.

* De computer die is geconfigureerd om de Hybrid Runbook Worker-functie uit te voeren, voldoet niet aan de minimale hardwarevereisten.

#### <a name="resolution"></a>Oplossing

Controleer of de computer uitgaande toegang heeft tot *. azure-automation.net op poort 443.

Computers die de Hybrid Runbook Worker uitvoeren, moeten voldoen aan de minimale hardwarevereisten voordat de werk nemer is geconfigureerd voor het hosten van deze functie. Runbooks en het achtergrond proces dat door ze worden gebruikt, kunnen ertoe leiden dat het systeem wordt gebruikt en vertraging of time-outs veroorzaakt.

Controleer of de computer waarop de Hybrid Runbook Worker-functie moet worden uitgevoerd, voldoet aan de minimale hardwarevereisten. Als dit het geval is, controleert u het CPU-en geheugen gebruik om de correlatie tussen de prestaties van Hybrid Runbook Worker processen en Windows te bepalen. Elk geheugen of CPU-druk kan erop wijzen dat er resources moeten worden bijgewerkt. U kunt ook een andere compute-resource selecteren die ondersteuning biedt voor de minimale vereisten en schalen wanneer de werk belasting vereist dat een toename nood zakelijk is.

Raadpleeg het **micro soft-SMA-** gebeurtenis logboek voor een bijbehorende gebeurtenis met de beschrijving *Win32-proces is afgesloten met code [4294967295]* . De oorzaak van deze fout is dat u geen authenticatie hebt geconfigureerd in uw runbooks of dat u de run as-referenties voor de groep Hybrid Runbook Worker hebt opgegeven. Controleer de runbook-machtigingen voor het [uitvoeren van runbooks op een Hybrid Runbook worker](../automation-hrw-run-runbooks.md) om te bevestigen dat u verificatie voor uw runbooks juist hebt geconfigureerd.

### <a name="no-cert-found"></a>Scenario: er is geen certificaat gevonden in het certificaat archief op Hybrid Runbook Worker

#### <a name="issue"></a>Probleem

Een runbook dat wordt uitgevoerd op een Hybrid Runbook Worker mislukt met het volgende fout bericht.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een [uitvoeren als-account](../manage-runas-account.md) probeert te gebruiken in een runbook dat wordt uitgevoerd op een Hybrid Runbook worker waarbij het certificaat van het run as-account niet aanwezig is. Hybrid Runbook Workers beschikken standaard niet over het lokale certificaat, dat vereist is voor het uitvoeren als-account.

#### <a name="resolution"></a>Oplossing

Als uw Hybrid Runbook Worker een Azure-VM is, kunt u in plaats daarvan [beheerde identiteiten voor Azure-resources](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) gebruiken. Dit scenario vereenvoudigt verificatie door u in plaats van het run as-account te verifiëren bij Azure-resources met behulp van de beheerde identiteit van de virtuele Azure-machine. Wanneer de Hybrid Runbook Worker een on-premises machine is, moet u het certificaat van het uitvoeren als-account op de computer installeren. Zie de stappen voor het uitvoeren van de Power shell-RunAsCertificateToHybridWorker in [runbooks uitvoeren op een Hybrid Runbook worker](../automation-hrw-run-runbooks.md)voor meer informatie over het installeren van het certificaat.

### <a name="error-403-on-registration"></a>Scenario: fout 403 tijdens de registratie van Hybrid Runbook Worker

#### <a name="issue"></a>Probleem

De eerste registratie fase van de werk nemer mislukt en u ontvangt de volgende fout (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Oorzaak

Mogelijke oorzaken zijn:
* Er is een niet-getypte werk ruimte-ID of werkruimte sleutel (primair) aanwezig in de instellingen van de agent. 
* De Hybrid Runbook Worker kan de configuratie niet downloaden, waardoor een fout met een account wordt gekoppeld. Wanneer Azure oplossingen biedt, worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Het is ook mogelijk dat er een onjuiste datum en/of tijd op de computer is ingesteld. Als de tijd +/-15 minuten vanaf de huidige tijd is, mislukt de onboarding.

#### <a name="resolution"></a>Oplossing

##### <a name="mistyped-workspace-idkey"></a>Niet-getypte werk ruimte-ID/sleutel
Als u wilt controleren of de werk ruimte-ID of de werk ruimte sleutel van de agent is getypt, raadpleegt u [een werk ruimte toevoegen of verwijderen: Windows agent](../../azure-monitor/platform/agent-manage.md#windows-agent) voor de Windows-agent of [een werk ruimte toevoegen of verwijderen. Dit is een Linux-agent](../../azure-monitor/platform/agent-manage.md#linux-agent) voor de Linux-agent.  Zorg ervoor dat u de volledige teken reeks selecteert in het Azure Portal en kopieer en plak deze zorgvuldig.

##### <a name="configuration-not-downloaded"></a>De configuratie is niet gedownload

Uw Log Analytics-werk ruimte en het Automation-account moeten zich in een gekoppelde regio bevinden. Zie [Azure Automation en log Analytics werkruimte toewijzingen](../how-to/region-mappings.md)voor een lijst met ondersteunde regio's.

Mogelijk moet u ook de datum en/of tijd zone van uw computer bijwerken. Als u een aangepast tijds bereik selecteert, moet u ervoor zorgen dat het bereik zich in UTC bevindt. Dit kan afwijken van uw lokale tijd zone.

## <a name="linux"></a>Linux

De Linux-Hybrid Runbook Worker is afhankelijk van de [log Analytics-agent voor Linux](../../azure-monitor/platform/log-analytics-agent.md) om te communiceren met uw Automation-account om de werk nemer te registreren, Runbook-taken te ontvangen en de status van het rapport. Als de registratie van de werk nemer mislukt, zijn hier enkele mogelijke oorzaken voor de fout:

### <a name="oms-agent-not-running"></a>Scenario: de Log Analytics-agent voor Linux wordt niet uitgevoerd

#### <a name="issue"></a>Probleem

De Log Analytics-agent voor Linux wordt niet uitgevoerd

#### <a name="cause"></a>Oorzaak

Als de agent niet wordt uitgevoerd, voor komt u dat de Linux-Hybrid Runbook Worker communiceert met Azure Automation. De agent kan om verschillende redenen niet worden uitgevoerd.

#### <a name="resolution"></a>Oplossing

 Controleer of de agent wordt uitgevoerd door de volgende opdracht in te voeren: `ps -ef | grep python`. De uitvoer ziet er ongeveer als volgt uit: de python-processen met **nxautomation** -gebruikers account. Als de Updatebeheer-of Azure Automation oplossingen niet zijn ingeschakeld, worden er geen van de volgende processen uitgevoerd.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

De volgende lijst bevat de processen die zijn gestart voor een Linux-Hybrid Runbook Worker. Ze bevinden zich allemaal in de `/var/opt/microsoft/omsagent/state/automationworker/` Directory.

* **OMS. conf** -het worker-proces. Het wordt direct vanuit DSC gestart.

* **Worker. conf** -het automatische geregistreerde Hybrid worker-proces. Het is gestart door de worker Manager. Dit proces wordt gebruikt door Updatebeheer en is transparant voor de gebruiker. Dit proces is niet aanwezig als de Updatebeheer-oplossing niet is ingeschakeld op de computer.

* **zelf/Worker. conf** -het proces van de zelf Hybrid Worker. Het zelf Hybrid worker-werk proces wordt gebruikt om gebruikers runbooks uit te voeren op de Hybrid Runbook Worker. Dit verschilt alleen van het automatische geregistreerde Hybrid worker-proces in de sleutel Details waarvoor een andere configuratie wordt gebruikt. Dit proces is niet aanwezig als de Azure Automation oplossing is uitgeschakeld en de zelf Linux-Hybrid Worker niet is geregistreerd.

Als de agent niet wordt uitgevoerd, voert u de volgende opdracht uit om de service te starten: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scenario: de opgegeven klasse bestaat niet

Als u de fout ziet **, bestaat de opgegeven klasse niet.** in het `/var/opt/microsoft/omsconfig/omsconfig.log`moet de Log Analytics-agent voor Linux worden bijgewerkt. Voer de volgende opdracht uit om de agent opnieuw te installeren:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

De Windows-Hybrid Runbook Worker is afhankelijk van de [log Analytics-agent voor Windows](../../azure-monitor/platform/log-analytics-agent.md) om te communiceren met uw Automation-account om de werk nemer te registreren, Runbook-taken te ontvangen en de status van het rapport. Als de registratie van de werk nemer mislukt, zijn hier enkele mogelijke oorzaken voor de fout:

### <a name="mma-not-running"></a>Scenario: micro soft monitoring agent wordt niet uitgevoerd

#### <a name="issue"></a>Probleem

De `healthservice`-service wordt niet uitgevoerd op de Hybrid Runbook Worker computer.

#### <a name="cause"></a>Oorzaak

Als micro soft Monitoring Agent micro soft-service niet wordt uitgevoerd, wordt door deze status voor komen dat de Hybrid Runbook Worker communiceert met Azure Automation.

#### <a name="resolution"></a>Oplossing

Controleer of de agent wordt uitgevoerd door de volgende opdracht in te voeren in Power shell: `Get-Service healthservice`. Als de service is gestopt, voert u de volgende opdracht in Power shell in om de service te starten: `Start-Service healthservice`.

### <a name="event-4502"></a>Scenario: gebeurtenis 4502 in Operations Manager logboek

#### <a name="issue"></a>Probleem

In het gebeurtenis logboek van de **toepassing en het service servicelogboeken\operations-beheer** ziet u gebeurtenis 4502 en EventMessage die **micro soft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent** bevat met de volgende beschrijving: *het certificaat dat wordt aangeboden door de service \<wsid\>. OMS.opinsights.Azure.com is niet uitgegeven door een certificerings instantie die wordt gebruikt voor micro soft-Services. Neem contact op met uw netwerk beheerder om te zien of er een proxy wordt uitgevoerd waarmee TLS/SSL-communicatie wordt onderschept.*

#### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door uw proxy of netwerk firewall die communicatie met Microsoft Azure blokkeert. Controleer of de computer uitgaande toegang heeft tot *. azure-automation.net op de poorten 443. 

#### <a name="resolution"></a>Oplossing

Logboeken worden lokaal opgeslagen op elke Hybrid worker op C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. U kunt controleren of er waarschuwingen of fouten zijn in het gebeurtenis logboek van de **toepassing en het Services-Logs\Microsoft-SMA\Operations** en de **toepassing en services van servicelogboeken\operations Manager** die duiden op een connectiviteit of een ander probleem dat van invloed is op de onboarding van de rol aan Azure Automation of probleem tijdens normale bewerkingen. Zie problemen [met de log Analytics Windows-agent oplossen](../../azure-monitor/platform/agent-windows-troubleshoot.md)voor meer informatie over het oplossen van problemen met de log Analytics-agent.

[Runbook-uitvoer en-berichten](../automation-runbook-output-and-messages.md) worden naar Azure Automation verzonden vanuit Hybrid Workers, net zoals met runbook-taken die worden uitgevoerd in de Cloud. U kunt de uitgebreide en voortgangs stromen ook op dezelfde manier inschakelen als voor andere runbooks.

### <a name="corrupt-cache"></a>Scenario: Hybrid Runbook Worker niet rapporteren

#### <a name="issue"></a>Probleem

Uw Hybrid Runbook Worker machine wordt uitgevoerd, maar er worden geen heartbeat-gegevens weer gegeven voor de machine in de werk ruimte.

In het volgende voor beeld ziet u de computers in een werk ruimte en de laatste heartbeat:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door een beschadigde cache op het Hybrid Runbook Worker.

#### <a name="resolution"></a>Oplossing

Om dit probleem op te lossen, meldt u zich aan bij de Hybrid Runbook Worker en voert u het volgende script uit. Met dit script wordt de micro soft Monitoring Agent gestopt, wordt de cache verwijderd en wordt de service opnieuw gestart. Deze actie zorgt ervoor dat de Hybrid Runbook Worker de configuratie van Azure Automation opnieuw te downloaden.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Scenario: u kunt geen Hybrid Runbook Worker toevoegen

#### <a name="issue"></a>Probleem

Het volgende bericht wordt weer gegeven wanneer u probeert een Hybrid Runbook Worker toe te voegen met behulp van de `Add-HybridRunbookWorker`-cmdlet.

```error
Machine is already registered
```

#### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als de computer al is geregistreerd met een ander Automation-account of als u de Hybrid Runbook Worker wilt lezen nadat u deze van een computer hebt verwijderd.

#### <a name="resolution"></a>Oplossing

U kunt dit probleem oplossen door de volgende register sleutel te verwijderen en de `HealthService` opnieuw te starten en de `Add-HybridRunbookWorker` cmdlet opnieuw uit te voeren:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts op [Azure-Forums](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure-account voor het verbeteren van de gebruikerservaring door de Azure-community in contact te brengen met de juiste resources: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.