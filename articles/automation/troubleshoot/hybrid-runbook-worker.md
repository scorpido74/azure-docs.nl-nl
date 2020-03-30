---
title: Probleemoplossing - Azure Automation Hybrid Runbook Workers
description: In dit artikel vindt u informatie over het oplossen van problemen met Azure Automation Hybrid Runbook Workers
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 33e3e162892f1e2a148258273160ca26fa9c2efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153519"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Problemen met hybride runbook-werknemers oplossen

In dit artikel vindt u informatie over het oplossen van problemen met hybride runbook-werknemers.

## <a name="general"></a>Algemeen

De Hybride Runbook Worker is afhankelijk van een agent die met uw Automatiseringsaccount communiceert om de werknemer te registreren, runbook-taken te ontvangen en de status van rapport te rapporteren. Voor Windows is deze agent de Log Analytics-agent voor Windows, ook wel Microsoft Monitoring Agent (MMA) genoemd. Voor Linux is het de Log Analytics-agent voor Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenario: Runbook-uitvoering mislukt

#### <a name="issue"></a>Probleem

Runbook uitvoering mislukt en u ontvangt de volgende fout.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Uw runbook wordt kort na pogingen om drie keer uit te voeren opgeschort. Er zijn voorwaarden die het runbook kunnen onderbreken. Het gerelateerde foutbericht bevat mogelijk geen aanvullende informatie.

#### <a name="cause"></a>Oorzaak

De volgende oorzaken zijn:

* De runbooks kunnen niet verifiëren met lokale bronnen.

* De hybride werknemer bevindt zich achter een proxy of firewall.

* De computer die is geconfigureerd om de functie Hybride runbookworker uit te voeren, voldoet niet aan de minimale hardwarevereisten.

#### <a name="resolution"></a>Oplossing

Controleer of de computer uitgaande toegang heeft tot ***.azure-automation.net** op poort 443.

Computers waarop de hybride runbookworker wordt uitgevoerd, moeten voldoen aan de minimale hardwarevereisten voordat de werknemer is geconfigureerd om deze functie te hosten. Runbooks en het achtergrondproces dat ze gebruiken, kunnen ertoe leiden dat het systeem te veel wordt gebruikt en dat het vertragingen of time-outs van runbook-taken veroorzaakt.

Controleer of de computer die de functie Hybride Runbook Worker wilt uitvoeren, voldoet aan de minimale hardwarevereisten. Als dit het geval is, controleert u het cpu- en geheugengebruik om de correlatie tussen de prestaties van hybride runbookworker-processen en Windows te bepalen. Elk geheugen of CPU-druk kan aangeven dat resources moeten worden geüupgradet. U ook een andere compute resource selecteren die de minimale vereisten en schaal ondersteunt wanneer workloadvereisten aangeven dat een verhoging noodzakelijk is.

Controleer het **Microsoft-SMA-gebeurtenislogboek** op `Win32 Process Exited with code [4294967295]`een overeenkomstige gebeurtenis met beschrijving . De oorzaak van deze fout is dat u verificatie niet hebt geconfigureerd in uw runbooks of de statusvan de groep Run As hebt opgegeven voor de groep Hybride runbookworker. Controleer runbook-machtigingen in [Runbooks uitvoeren op een hybride runbookworker](../automation-hrw-run-runbooks.md) om te controleren of u de verificatie voor uw runbooks correct hebt geconfigureerd.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenario: Gebeurtenis 15011 in hybride runbookworker

#### <a name="issue"></a>Probleem

De hybride runbookworker ontvangt gebeurtenis 15011, wat aangeeft dat een queryresultaat niet geldig is. De volgende fout wordt weergegeven wanneer de werknemer probeert een verbinding met de [SignalR-server](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1)te openen.

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Oorzaak

De hybride runbookworker is niet correct geconfigureerd voor de oplossing voor geautomatiseerde implementatie. Deze oplossing bevat een onderdeel dat de VM verbindt met de werkruimte Log Analytics. Het PowerShell-script zoekt naar de werkruimte in het abonnement met de meegeleverde naam. In dit geval heeft de werkruimte Log Analytics een ander abonnement. Het script kan de werkruimte niet vinden en probeert er een te maken, maar de naam is al gemaakt. De implementatie mislukt dus.

#### <a name="resolution"></a>Oplossing

U hebt twee opties om dit probleem op te lossen:

* Wijzig het PowerShell-script om te zoeken naar de werkruimte Log Analytics in een ander abonnement. Dit is een goede oplossing als u van plan bent om in de toekomst veel hybride runbook worker-machines te implementeren.

* Configureer de werkmachine handmatig om te draaien in een Orchestrator-sandbox. Voer vervolgens een runbook uit die is gemaakt in het Azure Automation-account van de werknemer om de functionaliteit te testen.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenario: Windows Azure VM's automatisch verwijderd uit hybride werknemersgroep

#### <a name="issue"></a>Probleem

U de hybride runbookworker of VM's niet zien wanneer de werkmachine lange tijd is uitgeschakeld.

#### <a name="cause"></a>Oorzaak

De machine Hybrid Runbook Worker heeft Azure Automation al meer dan 30 dagen niet gepingd. Als gevolg hiervan heeft Automation de groep Hybride runbookworker of de groep Systeemwerkgroep gezuiverd. 

#### <a name="resolution"></a>Oplossing

Start de werkmachine en registreer deze opnieuw bij Azure Automation. Zie instructies voor het installeren van de runbook-omgeving en het maken van verbinding met Azure Automation in [Implementeren van een Hybride Runbook Worker van Windows.](../automation-windows-hrw-install.md)

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenario: Er is geen certificaat gevonden in het certificaatarchief op Hybride Runbook Worker

#### <a name="issue"></a>Probleem

Een runbook dat wordt uitgevoerd op een hybride runbookworker, mislukt met het volgende foutbericht.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een [Run As-account](../manage-runas-account.md) probeert te gebruiken in een runbook dat wordt uitgevoerd op een hybride runbookworker waarbij het certificaat Uitvoeren als is uitgevoerd niet aanwezig is. Hybride runbook-werknemers hebben het certificaatitem niet standaard lokaal, wat vereist is door het Run As-account om goed te kunnen werken.

#### <a name="resolution"></a>Oplossing

Als uw Hybride Runbook Worker een Azure VM is, u [beheerde identiteiten gebruiken voor Azure-bronnen.](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) Dit scenario vereenvoudigt de verificatie doordat u verifiëren voor Azure-resources met behulp van de beheerde identiteit van de Azure VM in plaats van het Run As-account. Wanneer de hybride runbookworker een on-premises machine is, moet u het certificaat Uitvoeren als op de machine installeren. Zie de stappen voor het uitvoeren van de PowerShell runbook Export-RunAsCertificateToHybridWorker in [Runbooks op een hybride runbook worker](../automation-hrw-run-runbooks.md)voor meer informatie over het installeren van het certificaat.

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenario: Fout 403 tijdens registratie van hybride runbook worker

#### <a name="issue"></a>Probleem

De initiële registratiefase van de werknemer mislukt en u ontvangt de volgende fout (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Oorzaak

De volgende oorzaken zijn:
* Er is een verkeerd getypte werkruimte-id of werkruimtesleutel (primair) in de instellingen van de agent. 
* De hybride runbookworker kan de configuratie niet downloaden, waardoor er een fout optreedt bij het koppelen van accounts. Wanneer Azure oplossingen inschakelt, ondersteunt het alleen bepaalde regio's voor het koppelen van een Log Analytics-werkruimte en een Automatiseringsaccount. Het is ook mogelijk dat er een onjuiste datum en/of tijd op de computer is ingesteld. Als de tijd +/-15 minuten van de huidige tijd is, mislukt de onboarding.

#### <a name="resolution"></a>Oplossing

##### <a name="mistyped-workspace-idkey"></a>Verkeerd getypte werkruimte-id/toets
Zie Een werkruimte toevoegen of verwijderen als u wilt controleren of de werkruimte-id of werkruimtesleutel van de agent verkeerd is [getypt: Een werkruimte toevoegen of verwijderen : Windows-agent](../../azure-monitor/platform/agent-manage.md#windows-agent) voor de [Windows-agent](../../azure-monitor/platform/agent-manage.md#linux-agent) of Een werkruimte toevoegen of verwijderen - voor de Linux-agent.  Selecteer de volledige tekenreeks in de Azure-portal en kopieer en plak deze zorgvuldig.

##### <a name="configuration-not-downloaded"></a>Configuratie niet gedownload

Uw Log Analytics-werkruimte en automatiseringsaccount moeten zich in een gekoppelde regio bevinden. Zie [Azure Automation and Log Analytics workspace mappings](../how-to/region-mappings.md)voor een lijst met ondersteunde regio's.

Mogelijk moet u ook de datum en of tijdzone van uw computer bijwerken. Als u een aangepast tijdsbereik selecteert, moet u ervoor zorgen dat het bereik zich in UTC bevindt, wat kan verschillen van uw lokale tijdzone.

## <a name="linux"></a>Linux

De Linux Hybrid Runbook Worker is afhankelijk van de [Agent Log Analytics voor Linux](../../azure-monitor/platform/log-analytics-agent.md) om te communiceren met uw Automatiseringsaccount om de werknemer te registreren, runbook-taken te ontvangen en de status van rapport te rapporteren. Als de registratie van de werknemer mislukt, zijn hier enkele mogelijke oorzaken voor de fout:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenario: De Log Analytics-agent voor Linux wordt niet uitgevoerd

#### <a name="issue"></a>Probleem

De Log Analytics-agent voor Linux wordt niet uitgevoerd

#### <a name="cause"></a>Oorzaak

Als de agent niet wordt uitgevoerd, voorkomt dit dat de Linux Hybrid Runbook Worker kan communiceren met Azure Automation. De agent loopt mogelijk niet om verschillende redenen.

#### <a name="resolution"></a>Oplossing

 Controleer of de agent wordt `ps -ef | grep python`uitgevoerd door de opdracht in te voeren. U ziet uitvoer vergelijkbaar met de volgende, de Python processen met **nxautomation** gebruikersaccount. Als de oplossing Updatebeheer of Azure Automation niet is ingeschakeld, worden geen van de volgende processen uitgevoerd.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

In de volgende lijst worden de processen weergegeven die zijn gestart voor een Linux Hybrid Runbook Worker. Ze bevinden zich allemaal in de **/var/opt/microsoft/omsagent/state/automationworker/directory.**

* **oms.conf** - Het proces van de werknemermanager. Het is direct begonnen bij DSC.

* **worker.conf** - Het proces voor hybride werknemers met automatisch geregistreerd. Het is begonnen door de werknemersmanager. Dit proces wordt gebruikt door Update Management en is transparant voor de gebruiker. Dit proces is niet aanwezig als de oplossing Updatebeheer niet is ingeschakeld op de machine.

* **diy/worker.conf** - Het doe-het-zelf hybride werkproces. Het doe-het-zelf hybride werkproces wordt gebruikt om gebruikerslogboeken uit te voeren op de Hybride Runbook Worker. Het verschilt alleen van het proces van hybride werknemer met automatisch geregistreerd in de belangrijkste details dat het een andere configuratie gebruikt. Dit proces is niet aanwezig als de Azure Automation-oplossing is uitgeschakeld en de DIY Linux Hybrid Worker niet is geregistreerd.

Als de agent niet wordt uitgevoerd, voert u `sudo /opt/microsoft/omsagent/bin/service_control restart`de volgende opdracht uit om de service te starten: .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenario: de opgegeven klasse bestaat niet

Als u de `The specified class does not exist..` fout ziet in de **/var/opt/microsoft/omsconfig/omsconfig.log,** moet de Log Analytics-agent voor Linux worden bijgewerkt. Voer de volgende opdracht uit om de agent opnieuw te installeren:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

De Windows Hybrid Runbook Worker is afhankelijk van de [agent Log Analytics voor Windows](../../azure-monitor/platform/log-analytics-agent.md) om te communiceren met uw Automatiseringsaccount om de werknemer te registreren, runbook-taken te ontvangen en de status van rapport te rapporteren. Als de registratie van de werknemer mislukt, bevat deze sectie een aantal mogelijke redenen.

### <a name="scenario-the-microsoft-monitoring-agent-isnt-running"></a><a name="mma-not-running"></a>Scenario: de Microsoft-bewakingsagent wordt niet uitgevoerd

#### <a name="issue"></a>Probleem

De `healthservice` service wordt niet uitgevoerd op de hybride runbook worker-machine.

#### <a name="cause"></a>Oorzaak

Als de Microsoft Monitoring Agent-service niet wordt uitgevoerd, kan de hybride runbookworker niet communiceren met Azure Automation.

#### <a name="resolution"></a>Oplossing

Controleer of de agent wordt uitgevoerd door de `Get-Service healthservice`volgende opdracht in PowerShell in te voeren: . Als de service is gestopt, voert u de volgende `Start-Service healthservice`opdracht in PowerShell in om de service te starten: .

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Scenario: Gebeurtenis 4502 in het logboek operations manager

#### <a name="issue"></a>Probleem

In het **gebeurtenislogboek van Application and Services\Operations Manager** ziet u gebeurtenisgebeurtenis4502 en EventMessage `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` met de volgende beschrijving:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt doordat uw proxy- of netwerkfirewall de communicatie met Microsoft Azure blokkeert. Controleer of de computer uitgaande toegang heeft tot ***.azure-automation.net** op poort 443. 

#### <a name="resolution"></a>Oplossing

Logboeken worden lokaal opgeslagen op elke hybride werknemer bij **C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**. U controleren of er waarschuwings- of foutgebeurtenissen zijn in de **logboeken van toepassing en services\Microsoft-SMA\Operations** and **Application and Services Logs\Operations** Manager-gebeurtenislogboeken. Deze logboeken geven een verbindings- of ander type probleem aan dat van invloed is op de onboarding van de rol van Azure Automation of een probleem dat is ondervonden bij normale bewerkingen. Zie [Problemen met de Windows-agent Log Analytics oplossen](../../azure-monitor/platform/agent-windows-troubleshoot.md)voor aanvullende problemen met de log-analyse-agent.

Hybride werknemers verzenden [Runbook-uitvoer en -berichten](../automation-runbook-output-and-messages.md) naar Azure Automation op dezelfde manier als runbook-taken die in de cloud worden uitgevoerd, uitvoer uitvoer en berichten verzenden. U de Verbose- en Progress-streams inschakelen, net zoals u dat doet voor runbooks.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenario: Hybride runbookworker niet rapporteren

#### <a name="issue"></a>Probleem

Uw hybride runbookworker-machine wordt uitgevoerd, maar u ziet geen hartslaggegevens voor de machine in de werkruimte.

In de volgende voorbeeldquery worden de machines in een werkruimte en hun laatste hartslag weergegeven:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door een corrupte cache op de hybride runbookworker.

#### <a name="resolution"></a>Oplossing

Als u dit probleem wilt oplossen, meldt u zich aan bij de hybride runbookworker en voert u het volgende script uit. Met dit script wordt de Microsoft Monitoring Agent gestopt, wordt de cache verwijderd en wordt de service opnieuw gestart. Met deze actie dwingt de Hybride Runbook Worker om de configuratie opnieuw te downloaden van Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: U geen hybride runbookworker toevoegen

#### <a name="issue"></a>Probleem

U ontvangt het volgende bericht wanneer u een `Add-HybridRunbookWorker` hybride runbookworker probeert toe te voegen met de cmdlet.

```error
Machine is already registered
```

#### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt als de machine al is geregistreerd bij een ander Automatiseringsaccount of als u de Hybride Runbook Worker probeert te lezen nadat u deze uit een machine hebt verwijderd.

#### <a name="resolution"></a>Oplossing

Als u dit probleem wilt oplossen, `HealthService`verwijdert u `Add-HybridRunbookWorker` de volgende registersleutel, start u de cmdlet opnieuw en probeert u de cmdlet opnieuw.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met – het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, u een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.