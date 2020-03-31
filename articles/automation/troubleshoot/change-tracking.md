---
title: Problemen oplossen met Azure Change Tracking
description: Meer informatie over het oplossen en oplossen van problemen met de functie Azure Automation Change Tracking and Inventory.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198527"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Problemen met Wijzigingen bijhouden en Inventaris oplossen

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenario: Trackingrecords wijzigen worden niet weergegeven voor Windows-machines

#### <a name="issue"></a>Probleem

U ziet geen wijzigingenbijhouden of voorraadresultaten voor Windows-machines die zijn aanboord voor Change Tracking.

#### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

* De Microsoft Monitoring Agent wordt niet uitgevoerd.
* De communicatie naar het Automation-account wordt geblokkeerd.
* De beheerpakketten voor Change Tracking worden niet gedownload.
* De VM wordt aan boord kan afkomstig zijn van een gekloonde machine die niet was sysprepped met de Microsoft Monitoring Agent geïnstalleerd.

#### <a name="resolution"></a>Oplossing

De onderstaande oplossingen kunnen helpen bij het oplossen van uw probleem. Als u nog steeds hulp nodig hebt, u diagnostische informatie verzamelen en contact opnemen met ondersteuning. Navigeer op de agentmachine naar C:\Program Files\Microsoft Monitoring Agent\Agent\Tools en voer de volgende opdrachten uit:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Standaard is fouttracering ingeschakeld. Als u verbose foutberichten wilt inschakelen zoals in het voorgaande voorbeeld, gebruikt u de parameter *VER.* Gebruik *INF voor* informatiesporen wanneer u een beroep doet op **StartTracing.cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent wordt niet uitgevoerd

Controleer of de Microsoft Monitoring Agent (HealthService.exe) op de machine wordt uitgevoerd.

##### <a name="communication-to-automation-account-blocked"></a>Communicatie naar automatiseringsaccount geblokkeerd

Controleer Logboeken op de machine en zoek naar gebeurtenissen met het woord 'changetracking' in de machine.

Zie [Resources automatiseren in uw datacenter of cloud met Hybride runbookworker](../automation-hybrid-runbook-worker.md#network-planning) om meer te weten te komen over adressen en poorten die moeten worden toegestaan om Change Tracking te laten werken.

##### <a name="management-packs-not-downloaded"></a>Beheerpakketten niet gedownload

Controleer of de volgende pakketten voor het bijhouden en beheren van wijzigingen lokaal zijn geïnstalleerd:

* Microsoft.IntelligencePacks.changetrackingdirectagent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM van gekloonde machine die niet is gesysprepped

Als u een gekloonde afbeelding gebruikt, moet u de afbeelding eerst voorbereiden en vervolgens de Microsoft Monitoring Agent installeren.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Scenario: Geen resultaten voor het bijhouden van wijzigingen of voorraad op Linux-machines

#### <a name="issue"></a>Probleem

U ziet geen voorraad- of wijzigingstrackingresultaten voor Linux-machines die zijn aanboord voor Change Tracking. 

#### <a name="cause"></a>Oorzaak
Hier zijn mogelijke oorzaken specifiek voor dit probleem:
* De Log Analytics-agent voor Linux wordt niet uitgevoerd.
* De Log Analytics-agent voor Linux is niet correct geconfigureerd.
* Er zijn FIM-conflicten (File Integrity Monitoring).

#### <a name="resolution"></a>Oplossing 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics-agent voor Linux wordt niet uitgevoerd

Controleer of de daemon voor de Log Analytics-agent voor Linux (omsagent) op uw machine wordt uitgevoerd. Voer de volgende query uit in de werkruimte Log Analytics die is gekoppeld aan uw Automatiseringsaccount.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Als u uw machine niet ziet in queryresultaten, is deze niet onlangs ingecheckt. Er is waarschijnlijk een lokaal configuratieprobleem en u moet de agent opnieuw installeren. Zie [Logboekgegevens verzamelen met de loganalyse-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)voor informatie over installatie en configuratie. 

Als uw machine wordt weergegeven in de queryresultaten, controleert u de scopeconfiguratie. Zie [Oplossingen voor targetingbewaking in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Voor meer problemen met dit probleem raadpleegt u [Probleem: U ziet geen Linux-gegevens.](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics-agent voor Linux niet correct geconfigureerd

De loganalyse-agent voor Linux is mogelijk niet correct geconfigureerd voor het verzamelen van logboek- en opdrachtregeluitvoer met behulp van het gereedschap OMS-logboekverzamelaar. Zie [Wijzigingen in uw omgeving bijhouden met de oplossing Wijzigingstracking.](../change-tracking.md)

##### <a name="fim-conflicts"></a>FIM-conflicten

De FIM-functie van Azure Security Center valideert mogelijk ten onrechte de integriteit van uw Linux-bestanden. Controleer of FIM operationeel en correct is geconfigureerd voor Linux-bestandsbewaking. Zie [Wijzigingen in uw omgeving bijhouden met de oplossing Wijzigingstracking.](../change-tracking.md)

## <a name="next-steps"></a>Volgende stappen

Als u uw probleem niet ziet of niet in staat bent om uw probleem op te lossen, gebruikt u een van de volgende kanalen voor meer ondersteuning.

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met – het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, u een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
