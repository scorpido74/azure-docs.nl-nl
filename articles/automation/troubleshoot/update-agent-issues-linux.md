---
title: Problemen met Linux-updateagentoplossen in Azure Automation Update Management
description: Meer informatie over het oplossen en oplossen van problemen met de Linux Windows-updateagent met behulp van de updatebeheeroplossing.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: bba1c7e89a9c3bb1c9aa1567e36dd71a40f14636
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679059"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Problemen met Linux-updateagentoplossen

Er kunnen vele redenen zijn waarom uw machine niet als klaar (gezond) wordt weergegeven in Update Management. In Updatebeheer u de status van een hybride runbookworker-agent controleren om het onderliggende probleem te bepalen. In dit artikel wordt besproken hoe u de probleemoplosser voor Azure-machines uitvoeren vanuit de Azure-portal en niet-Azure-machines in het [offlinescenario.](#troubleshoot-offline) 

De volgende lijst zijn de drie gereedheidstoestanden waarin een machine zich kan bevinden:

* Klaar - De hybride runbook worker is geïmplementeerd en is minder dan 1 uur geleden voor het laatst gezien.
* Losgekoppeld - De hybride runbookworker wordt geïmplementeerd en is meer dan 1 uur geleden voor het laatst gezien.
* Niet geconfigureerd - De hybride runbookworker is niet gevonden of is nog niet klaar met instappen.

> [!NOTE]
> Er kan een kleine vertraging optreden tussen wat de Azure-portal weergeeft en de huidige status van een machine.

## <a name="start-the-troubleshooter"></a>De probleemoplosser starten

Klik voor Azure-machines op de koppeling **Problemen oplossen** onder de kolom **Gereedheid bijwerken** in de portal en start de pagina Problemen met updateagent. Voor niet-Azure-machines brengt de koppeling u naar dit artikel. Zie de offline instructies om problemen op te lossen voor een niet-Azure-machine.

![vm-lijstpagina](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> De controles vereisen dat de VM wordt uitgevoerd. Als de vm niet wordt uitgevoerd, krijgt u een knop **Start de VM** weergegeven.

Klik op de pagina Problemen met bijwerken agent op **Controles uitvoeren**om de probleemoplosser te starten. De probleemoplosser gebruikt [de opdracht Uitvoeren](../../virtual-machines/linux/run-command.md) om een script op de machine uit te voeren om de afhankelijkheden te verifiëren. Wanneer de probleemoplosser is voltooid, wordt het resultaat van de controles geretourneerd.

![Problemen met de pagina oplossen](../media/update-agent-issues-linux/troubleshoot-page.png)

Als deze zijn voltooid, worden de resultaten geretourneerd in het venster. De controlesecties geven informatie over wat elke controle zoekt.

![Pagina controlevan agent bijwerken](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controles van vereisten

### <a name="operating-system"></a>Besturingssysteem

De controle van het besturingssysteem controleert of de hybride runbookworker een van de volgende besturingssystemen uitvoert:

|Besturingssysteem  |Opmerkingen  |
|---------|---------|
|CentOS 6 (x86/x64) en 7 (x64)      | Linux-agents moeten toegang hebben tot een opslagplaats voor updates. Classificatiegebaseerdpatchen vereist 'yum' om beveiligingsgegevens terug te sturen die CentOS niet uit de doos heeft.         |
|Red Hat Enterprise 6 (x86/x64) en 7 (x64)     | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|SUSE Linux Enterprise Server 11 (x86/x64) en 12 (x64)     | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|Ubuntu 14.04 LTS, 16.04 LTS en 18.04 LTS (x86/x64)      |Linux-agents moeten toegang hebben tot een opslagplaats voor updates.         |

## <a name="monitoring-agent-service-health-checks"></a>Monitoring agent service gezondheidscontroles

### <a name="log-analytics-agent"></a>Log Analytics-agent

Deze controle zorgt ervoor dat de Log Analytics-agent voor Linux is geïnstalleerd. Zie [De agent voor Linux installeren voor](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
)instructies over het installeren ervan.

### <a name="log-analytics-agent-status"></a>Status van log-analyse-agent

Deze controle zorgt ervoor dat de Log Analytics-agent voor Linux wordt uitgevoerd. Als de agent niet wordt uitgevoerd, u de volgende opdracht uitvoeren om te proberen deze opnieuw op te starten. Zie Problemen met de probleemoplossing voor [linux hybride runbook-werknemers voor](hybrid-runbook-worker.md#linux) meer informatie over het oplossen van problemen met de agent

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming (multihoming)

Deze controle bepaalt of de agent rapporteert aan meerdere werkruimten. Multi-homing wordt niet ondersteund door Update Management.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Deze controle controleert of de log-analyse-agent voor Linux het hybride runbookworker-pakket heeft. Dit pakket is vereist om Update Management te laten werken.

### <a name="hybrid-runbook-worker-status"></a>Status hybride runbookworker

Deze controle zorgt ervoor dat de hybride runbook worker op de machine wordt uitgevoerd. De volgende processen moeten aanwezig zijn als de hybride runbookworker correct wordt uitgevoerd. Zie het oplossen van problemen met [de Log Analytics Agent voor Linux voor](hybrid-runbook-worker.md#oms-agent-not-running)meer informatie.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Connectiviteitscontroles

### <a name="general-internet-connectivity"></a>Algemene internetverbinding

Deze controle zorgt ervoor dat de machine toegang heeft tot het internet.

### <a name="registration-endpoint"></a>Eindpunt voor registratie

Met deze controle wordt bepaald of de hybride runbookworker de werkruimte Log Analytics naar behoren kan communiceren met Azure Automation.

Proxy- en firewallconfiguraties moeten de medewerker Hybride Runbook Worker in staat stellen te communiceren met het registratieeindpunt. Zie [Netwerkplanning voor hybride werknemers voor](../automation-hybrid-runbook-worker.md#network-planning) een lijst met adressen en poorten die u wilt openen

### <a name="operations-endpoint"></a>Eindpunt voor bewerkingen

Deze controle bepaalt of de agent goed kan communiceren met de Job Runtime Data Service.

Proxy- en firewallconfiguraties moeten de medewerker Hybride Runbook Worker in staat stellen te communiceren met de Job Runtime Data Service. Zie [Netwerkplanning voor hybride werknemers voor](../automation-hybrid-runbook-worker.md#network-planning) een lijst met adressen en poorten die u wilt openen

### <a name="log-analytics-endpoint-1"></a>Eindpunt 1 logboekanalyse

Deze controle controleert of uw machine toegang heeft tot de eindpunten die de log analytics-agent nodig heeft.

### <a name="log-analytics-endpoint-2"></a>Eindpunt 2 van Log Analytics

Deze controle controleert of uw machine toegang heeft tot de eindpunten die de log analytics-agent nodig heeft.

### <a name="log-analytics-endpoint-3"></a>Eindpunt van Log Analytics 3

Deze controle controleert of uw machine toegang heeft tot de eindpunten die de log analytics-agent nodig heeft.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Problemen met offline oplossen

U de probleemoplosser offline gebruiken voor een hybride runbookworker door het script lokaal uit te voeren. Het python script, [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) is te vinden in het Script Center. Een voorbeeld van de uitvoer van dit script wordt weergegeven in het volgende voorbeeld:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Volgende stappen

Zie Problemen oplossen [met hybride runbookworkers](hybrid-runbook-worker.md)als u aanvullende problemen met uw hybride runbook-werknemers wilt oplossen.
