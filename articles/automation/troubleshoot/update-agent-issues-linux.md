---
title: Linux-Hybrid Runbook Worker diagnosticeren-Azure Updatebeheer
description: Meer informatie over het oplossen van problemen met het Azure Automation Hybrid Runbook Worker op Linux dat Updatebeheer ondersteunt.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: e60ba71607b99f0ea97e0725ffdd0740f3e9c579
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372962"
---
# <a name="understand-and-resolve-linux-hybrid-runbook-worker-health-for-update-management"></a>De status van Linux Hybrid Runbook Worker voor Updatebeheer begrijpen en oplossen

Er kunnen veel redenen zijn waarom uw computer niet **gereed** is in updatebeheer. In Updatebeheer kunt u de status van een Hybrid Runbook Worker agent controleren om het onderliggende probleem te bepalen. In dit artikel wordt beschreven hoe u de probleem oplosser voor Azure-machines uitvoert vanaf de Azure Portal-en niet-Azure-machines in het [offline scenario](#troubleshoot-offline).

De volgende lijst geeft een overzicht van de drie gereedheids statussen die een machine kan hebben:

* **Gereed** : de Hybrid Runbook Worker is geïmplementeerd en is minder dan 1 uur geleden voor het laatst gezien.
* De **verbinding is verbroken** : de Hybrid Runbook Worker is geïmplementeerd en is meer dan 1 uur geleden voor het laatst gezien.
* **Niet geconfigureerd** : de Hybrid Runbook Worker is niet gevonden of is niet gereed voor onboarding.

> [!NOTE]
> Er kan een lichte vertraging optreden tussen de Azure Portal weer geven en de huidige status van de machine.

## <a name="start-the-troubleshooter"></a>De probleemoplosser voor starten

Voor Azure-machines klikt u op de koppeling **problemen oplossen** onder de gereedheids kolom van de **Update Agent** in de portal, wordt de pagina **problemen met Update agent oplossen** gestart. Voor niet-Azure-computers brengt de koppeling u naar dit artikel. Zie de offline-instructies voor het oplossen van problemen met een niet-Azure-machine.

![de pagina van de VM-lijst](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> De controles moeten de virtuele machine worden uitgevoerd. Als de VM niet wordt uitgevoerd, wordt er een knop weer gegeven voor **het starten van de virtuele machine**.

Klik op de pagina **Update agent oplossen** op **controles uitvoeren**om de probleem oplosser te starten. De probleem Oplosser gebruikt de [opdracht uitvoeren](../../virtual-machines/linux/run-command.md) om een script uit te voeren op de machine om de afhankelijkheden te controleren. Wanneer de probleemoplosser voor voltooid is, wordt het resultaat van de controles.

![Pagina oplossen](../media/update-agent-issues-linux/troubleshoot-page.png)

Als u klaar bent, worden de resultaten worden geretourneerd in het venster. De controle secties bevatten informatie over wat elke controle zoekt.

![Update-agent controleert op pagina](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controles van vereisten

### <a name="operating-system"></a>Besturingssysteem

De controle van het besturings systeem controleert of op de Hybrid Runbook Worker een van de volgende besturings systemen wordt uitgevoerd:

|Besturingssysteem  |Opmerkingen  |
|---------|---------|
|CentOS 6 (x86/x64) en 7 (x64)      | Linux-agents moeten toegang hebben tot een opslagplaats voor updates. Voor op classificatie gebaseerde patches moet ' yum ' worden geretourneerd om beveiligings gegevens te retour neren die geen deel uitmaakt van het CentOS.         |
|Red Hat Enterprise 6 (x86/x64) en 7 (x64)     | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|SUSE Linux Enterprise Server 11 (x86/x64) en 12 (x64)     | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|Ubuntu 14.04 LTS, 16.04 LTS en 18.04 LTS (x86/x64)      |Linux-agents moeten toegang hebben tot een opslagplaats voor updates.         |

## <a name="monitoring-agent-service-health-checks"></a>Monitoring agent-service-statuscontroles

### <a name="log-analytics-agent"></a>Log Analytics-agent

Met deze controle wordt ervoor gezorgd dat de Log Analytics-agent voor Linux is geïnstalleerd. Zie voor instructies over het installeren van [de agent voor Linux installeren](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Status van Log Analytics agent

Met deze controle wordt ervoor gezorgd dat de Log Analytics-agent voor Linux wordt uitgevoerd. Als de agent niet wordt uitgevoerd, kunt u de volgende opdracht uitvoeren om het opnieuw te proberen. Zie voor meer informatie over het oplossen van problemen met de agent [Linux Hybrid Runbook worker Troubleshooting](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Deze controle wordt bepaald of de agent is rapporteert aan meerdere werkruimten. Multihoming wordt niet ondersteund door beheer van updates.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Met deze controle wordt gecontroleerd of de Log Analytics-agent voor Linux het Hybrid Runbook Worker-pakket heeft. Dit pakket is vereist voor het beheer van updates om te werken.

### <a name="hybrid-runbook-worker-status"></a>Hybrid Runbook Worker-status

Deze controle zorgt ervoor dat de Hybrid Runbook Worker wordt uitgevoerd op de machine. De volgende processen moet aanwezig zijn als de Hybrid Runbook Worker correct wordt uitgevoerd. Zie [problemen met de log Analytics-agent voor Linux oplossen](hybrid-runbook-worker.md#oms-agent-not-running)voor meer informatie.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Controles voor connectiviteit

### <a name="general-internet-connectivity"></a>Algemene verbinding met internet

Deze controle zorgt ervoor dat de computer toegang tot internet heeft.

### <a name="registration-endpoint"></a>Eindpunt voor clientregistratie

Met deze controle wordt bepaald of de Hybrid Runbook Worker goed kan communiceren met Azure Automation de Log Analytics-werk ruimte.

Proxy- en firewallinstellingen configuraties moeten de Hybrid Runbook Worker-agent kan communiceren met het eindpunt voor clientregistratie toestaan. Zie [netwerk planning voor Hybrid Workers](../automation-hybrid-runbook-worker.md#network-planning) voor een lijst met adressen en poorten die moeten worden geopend.

### <a name="operations-endpoint"></a>Operations-eindpunt

Deze controle bepaalt als de agent correct met de taak-Runtime-gegevens-Service communiceren kan.

Proxy- en firewallinstellingen configuraties moeten de Hybrid Runbook Worker-agent kan communiceren met de taak Runtime Data-Service toestaan. Zie [netwerk planning voor Hybrid Workers](../automation-hybrid-runbook-worker.md#network-planning) voor een lijst met adressen en poorten die moeten worden geopend.

### <a name="log-analytics-endpoint-1"></a>Log Analytics-eindpunt 1

Deze controle alleen gecontroleerd dat de computer toegang tot de eindpunten die nodig zijn voor de Log Analytics-agent heeft.

### <a name="log-analytics-endpoint-2"></a>Log Analytics-eindpunt 2

Deze controle alleen gecontroleerd dat de computer toegang tot de eindpunten die nodig zijn voor de Log Analytics-agent heeft.

### <a name="log-analytics-endpoint-3"></a>Log Analytics-eindpunt 3

Deze controle alleen gecontroleerd dat de computer toegang tot de eindpunten die nodig zijn voor de Log Analytics-agent heeft.

## <a name="troubleshoot-offline"></a>Problemen met offline oplossen

U kunt de probleemoplosser voor offline gebruiken op een Hybrid Runbook Worker door het script lokaal worden uitgevoerd. Het python-script, [update_mgmt_health_check. py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) , vindt u in Script Center. Een voorbeeld van de uitvoer van dit script wordt weergegeven in het volgende voorbeeld:

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

Zie [problemen oplossen-Hybrid Runbook Workers](hybrid-runbook-worker.md)voor meer informatie over het oplossen van extra problemen met uw Hybrid runbook Workers.
