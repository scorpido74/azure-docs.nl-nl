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
ms.openlocfilehash: 31d81c6946fc256f5c22b93674469d7b87500173
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480716"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Problemen met Hybrid Runbook Workers oplossen

Dit artikel bevat informatie over het oplossen van problemen met Hybrid Runbook Workers.

## <a name="general"></a>Algemeen

De Hybrid Runbook Worker is afhankelijk van een agent om te communiceren met uw Automation-account om de werk nemer te registreren, Runbook-taken te ontvangen en de status van het rapport. Voor Windows is deze agent de Log Analytics-agent voor Windows (ook wel micro soft Monitoring Agent (MMA) genoemd). Voor Linux is het de Log Analytics-agent voor Linux.

### <a name="runbook-execution-fails"></a>Scenario: het uitvoeren van een Runbook mislukt

#### <a name="issue"></a>Probleem

De Runbook-uitvoering is mislukt en de volgende fout wordt weer gegeven:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Uw runbook is onderbroken kort nadat dit drie keer is geprobeerd uit te voeren. Er zijn voor waarden, waardoor het runbook kan worden onderbroken. Als dit gebeurt, kan het gerelateerde fout bericht geen aanvullende informatie bevatten die u vertelt waarom.

#### <a name="cause"></a>Oorzaak

Hier volgen mogelijke oorzaken:

* De runbooks kunnen niet worden geverifieerd met lokale resources

* De Hybrid worker bevindt zich achter een proxy of firewall

* De runbooks kunnen niet worden geverifieerd met lokale resources

* De computer die is geconfigureerd om de Hybrid Runbook Worker-functie uit te voeren, voldoet niet aan de minimale hardwarevereisten.

#### <a name="resolution"></a>Oplossing

Controleer of de computer uitgaande toegang heeft tot *. azure-automation.net op poort 443.

Computers die de Hybrid Runbook Worker uitvoeren, moeten voldoen aan de minimale hardwarevereisten voordat deze wordt geconfigureerd voor het hosten van deze functie. Runbooks en de achtergrond processen die door ze worden gebruikt, kunnen ertoe leiden dat het systeem wordt gebruikt en vertragingen of time-outs van runbook-taken veroorzaken.

Controleer of de computer waarop de Hybrid Runbook Worker-functie wordt uitgevoerd, voldoet aan de minimale hardwarevereisten. Als dit het geval is, controleert u het CPU-en geheugen gebruik om de correlatie tussen de prestaties van Hybrid Runbook Worker processen en Windows te bepalen. Als er geheugen of CPU-druk is, kan dit wijzen op de nood zaak om resources bij te werken. U kunt ook een andere reken resource selecteren die de minimale vereisten ondersteunt en schalen wanneer de werkbelasting vraag aangeeft dat er een verhoging nodig is.

Raadpleeg het **micro soft-SMA-** gebeurtenis logboek voor een bijbehorende gebeurtenis met de beschrijving *Win32-proces is afgesloten met code [4294967295]* . De oorzaak van deze fout is dat u verificatie niet hebt geconfigureerd in uw runbooks of dat u de run as-referenties voor de Hybrid worker-groep hebt opgegeven. Controleer de [Runbook-machtigingen](../automation-hrw-run-runbooks.md#runbook-permissions) om te bevestigen dat u de verificatie voor uw runbooks juist hebt geconfigureerd.

### <a name="no-cert-found"></a>Scenario: er is geen certificaat gevonden in het certificaat archief op Hybrid Runbook Worker

#### <a name="issue"></a>Probleem

Een runbook dat wordt uitgevoerd op een Hybrid Runbook Worker mislukt met het volgende fout bericht:

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een [uitvoeren als-account](../manage-runas-account.md) probeert te gebruiken in een runbook dat wordt uitgevoerd op een Hybrid Runbook worker waarbij het certificaat van het run as-account niet aanwezig is. Hybrid Runbook Workers beschikken standaard niet over het lokale certificaat, wat vereist is voor een goede werking van het run as-account.

#### <a name="resolution"></a>Oplossing

Als uw Hybrid Runbook Worker een Azure-VM is, kunt u in plaats daarvan [beheerde identiteiten voor Azure-resources](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) gebruiken. Met dit scenario kunt u zich verifiëren bij Azure-resources met behulp van de beheerde identiteit van de virtuele Azure-machine in plaats van het run as-account, waardoor verificatie wordt vereenvoudigd. Wanneer de Hybrid Runbook Worker een on-premises machine is, moet u het certificaat van het uitvoeren als-account op de computer installeren. Zie voor meer informatie over het installeren van het certificaat de stappen voor het uitvoeren van het [export-RunAsCertificateToHybridWorker](../automation-hrw-run-runbooks.md#runas-script) runbook.

## <a name="linux"></a>Linux

De Linux-Hybrid Runbook Worker is afhankelijk van de [log Analytics-agent voor Linux](../../azure-monitor/platform/log-analytics-agent.md) om te communiceren met uw Automation-account om de werk nemer te registreren, Runbook-taken te ontvangen en de status van het rapport. Als de registratie van de werk nemer mislukt, zijn hier enkele mogelijke oorzaken voor de fout:

### <a name="oms-agent-not-running"></a>Scenario: de logboek Analyics-agent voor Linux wordt niet uitgevoerd

#### <a name="issue"></a>Probleem

De Log Analytics-agent voor Linux is niet actief

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


* **OMS. conf** -deze waarde is het Work Manager-proces. Het wordt direct vanuit DSC gestart.

* **werk nemer. conf** : dit proces is het automatische geregistreerde Hybrid worker-proces, dat door de worker Manager wordt gestart. Dit proces wordt gebruikt door Updatebeheer en is transparant voor de gebruiker. Dit proces is niet aanwezig als de Updatebeheer-oplossing niet is ingeschakeld op de computer.

* **zelf/Worker. conf** : dit proces is het proces van de zelf Hybrid Worker. Het zelf Hybrid worker-werk proces wordt gebruikt om gebruikers runbooks uit te voeren op de Hybrid Runbook Worker. Dit verschilt alleen van het automatische geregistreerde Hybrid worker-proces in de sleutel details die een andere configuratie gebruikt. Dit proces is niet aanwezig als de Azure Automation oplossing is uitgeschakeld en de zelf Linux-Hybrid Worker niet is geregistreerd.

Als de agent niet wordt uitgevoerd, voert u de volgende opdracht uit om de service te starten: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scenario: de opgegeven klasse bestaat niet

Als u de volgende fout ziet: **de opgegeven klasse bestaat niet.** in het `/var/opt/microsoft/omsconfig/omsconfig.log` moet de Log Analytics-agent voor Linux worden bijgewerkt. Voer de volgende opdracht uit om de agent opnieuw te installeren:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

De Windows-Hybrid Runbook Worker is afhankelijk van de [log Analytics-agent voor Windows](../../azure-monitor/platform/log-analytics-agent.md) om te communiceren met uw Automation-account om de werk nemer te registreren, Runbook-taken te ontvangen en de status van het rapport. Als de registratie van de werk nemer mislukt, zijn hier enkele mogelijke oorzaken voor de fout:

### <a name="mma-not-running"></a>Scenario: micro soft monitoring agent wordt niet uitgevoerd

#### <a name="issue"></a>Probleem

De `healthservice`-service wordt niet uitgevoerd op de Hybrid Runbook Worker computer.

#### <a name="cause"></a>Oorzaak

Als de Windows-service micro soft Monitoring Agent niet wordt uitgevoerd, wordt door deze status voor komen dat de Hybrid Runbook Worker communiceert met Azure Automation.

#### <a name="resolution"></a>Oplossing

Controleer of de agent wordt uitgevoerd door de volgende opdracht in te voeren in Power shell: `Get-Service healthservice`. Als de service is gestopt, voert u de volgende opdracht in Power shell in om de service te starten: `Start-Service healthservice`.

### <a name="event-4502"></a>Gebeurtenis 4502 in Operations Manager logboek

#### <a name="issue"></a>Probleem

In het gebeurtenis logboek van de **toepassing en het service servicelogboeken\operations-beheer** ziet u gebeurtenis 4502 en EventMessage die **micro soft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent** bevat met de volgende beschrijving: *het certificaat dat wordt aangeboden door de service \<wsid\>. OMS.opinsights.Azure.com is niet uitgegeven door een certificerings instantie die wordt gebruikt voor micro soft-Services. Neem contact op met uw netwerk beheerder om te zien of er een proxy wordt uitgevoerd waarmee TLS/SSL-communicatie wordt onderschept.*

#### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door uw proxy of netwerk firewall die communicatie met Microsoft Azure blokkeert. Controleer of de computer uitgaande toegang heeft tot *. azure-automation.net op de poorten 443. 

#### <a name="resolution"></a>Oplossing

Logboeken worden lokaal opgeslagen op elke Hybrid worker op C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. U kunt controleren of er waarschuwingen of fouten zijn in het gebeurtenis logboek van de **toepassing en het Services-Logs\Microsoft-SMA\Operations** en het **toepassings-en servicelogboeken\operations beheer** dat zou wijzen op een connectiviteit of een ander probleem dat van invloed is op de onboarding van de rol aan Azure Automation of probleem tijdens normale bewerkingen. Zie problemen [met de log Analytics Windows-agent oplossen](../../azure-monitor/platform/agent-windows-troubleshoot.md)voor meer informatie over het oplossen van problemen met de log Analytics-agent.

[Runbook-uitvoer en-berichten](../automation-runbook-output-and-messages.md) worden naar Azure Automation verzonden vanuit Hybrid Workers, net zoals met runbook-taken die worden uitgevoerd in de Cloud. U kunt de uitgebreide en voortgangs stromen ook op dezelfde manier inschakelen als voor andere runbooks.

### <a name="corrupt-cache"></a>Hybrid Runbook Worker niet rapporteren

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

Om dit probleem op te lossen, meldt u zich aan bij de Hybrid Runbook Worker en voert u het volgende script uit. Met dit script wordt de micro soft Monitoring Agent gestopt, wordt de cache verwijderd en wordt de service opnieuw gestart. Deze actie dwingt de Hybrid Runbook Worker de configuratie opnieuw te downloaden van Azure Automation.

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

Dit kan gebeuren als de computer al is geregistreerd met een ander Automation-account of als u de Hybrid Runbook Worker opnieuw wilt toevoegen nadat u deze van een computer hebt verwijderd.

#### <a name="resolution"></a>Oplossing

U kunt dit probleem oplossen door de volgende register sleutel te verwijderen en de `HealthService` opnieuw te starten en de `Add-HybridRunbookWorker` cmdlet opnieuw uit te voeren:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts op [Azure-Forums](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure-account voor het verbeteren van de gebruikerservaring door de Azure-community in contact te brengen met de juiste resources: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.

