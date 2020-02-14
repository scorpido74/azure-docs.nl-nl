---
title: Problemen met Azure Wijzigingen bijhouden oplossen
description: Meer informatie over het oplossen van problemen met de functie Wijzigingen bijhouden en de inventaris van Azure Automation.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198527"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Problemen met Wijzigingen bijhouden en Inventaris oplossen

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scenario: Wijzigingen bijhouden records niet worden weer gegeven voor Windows-computers

#### <a name="issue"></a>Probleem

U ziet geen Wijzigingen bijhouden of inventarisatie resultaten voor Windows-machines die zijn voor bereid op Wijzigingen bijhouden.

#### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

* Micro soft monitoring agent wordt niet uitgevoerd.
* De communicatie met het Automation-account wordt geblokkeerd.
* De Management Packs voor Wijzigingen bijhouden zijn niet gedownload.
* De virtuele machine die wordt vrijgegeven, kan afkomstig zijn van een gekloonde computer die niet is Sysprep met micro soft monitoring agent geïnstalleerd.

#### <a name="resolution"></a>Oplossing

De oplossingen die hieronder worden beschreven, kunnen helpen bij het oplossen van uw probleem. Als u nog steeds hulp nodig hebt, kunt u Diagnostische gegevens verzamelen en contact opnemen met ondersteuning. Ga op de agent computer naar C:\Program Files\Microsoft monitoring Agent\Agent\Tools en voer de volgende opdrachten uit:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Fout tracering is standaard ingeschakeld. Als u uitgebreide fout berichten wilt inschakelen, zoals in het voor gaande voor beeld, gebruikt u de *ver* -para meter. Gebruik voor informatie tracering *inf* bij het aanroepen van **StartTracing. cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Micro soft monitoring agent wordt niet uitgevoerd

Controleer of micro soft Monitoring Agent (HealthService. exe) wordt uitgevoerd op de computer.

##### <a name="communication-to-automation-account-blocked"></a>Communicatie met Automation-account geblokkeerd

Controleer Logboeken op de computer en zoek naar gebeurtenissen die het woord ' change tracking ' bevatten.

Zie [resources automatiseren in uw Data Center of in de Cloud met behulp van Hybrid Runbook worker](../automation-hybrid-runbook-worker.md#network-planning) voor meer informatie over adressen en poorten die moeten worden toegestaan om wijzigingen bijhouden te kunnen werken.

##### <a name="management-packs-not-downloaded"></a>Management Packs niet gedownload

Controleer of de volgende Wijzigingen bijhouden-en Inventory Management Packs lokaal zijn geïnstalleerd:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Micro soft. intelligence packs. InventoryChangeTracking. *
* Micro soft. intelligence packs. SingletonInventoryCollection. *

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM van een gekloonde computer die niet is Sysprep

Als u een gekloonde installatie kopie gebruikt, Sysprep de installatie kopie eerst en installeert u vervolgens micro soft monitoring agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Scenario: geen Wijzigingen bijhouden-of inventaris resultaten op Linux-machines

#### <a name="issue"></a>Probleem

U ziet geen inventaris-of Wijzigingen bijhouden resultaten voor Linux-machines die zijn voor bereid op Wijzigingen bijhouden. 

#### <a name="cause"></a>Oorzaak
Hier vindt u mogelijke oorzaken die specifiek zijn voor dit probleem:
* De Log Analytics-agent voor Linux wordt niet uitgevoerd.
* De Log Analytics-agent voor Linux is niet juist geconfigureerd.
* Er zijn FIM-conflicten (File Integrity Monitoring).

#### <a name="resolution"></a>Oplossing 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics-agent voor Linux niet actief

Controleer of de daemon voor de Log Analytics-agent voor Linux (omsagent) wordt uitgevoerd op de computer. Voer de volgende query uit in de Log Analytics-werk ruimte die is gekoppeld aan uw Automation-account.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Als uw machine niet in query resultaten wordt weer geven, is deze niet recent ingecheckt. Er is waarschijnlijk een probleem met de lokale configuratie en u moet de agent opnieuw installeren. Zie [logboek gegevens verzamelen met de log Analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)voor meer informatie over de installatie en configuratie. 

Als uw computer in de query resultaten wordt weer gegeven, controleert u de scope configuratie. Zie [oplossingen voor doel bewaking in azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Raadpleeg het volgende voor meer informatie over het oplossen van dit probleem [: u ziet geen Linux-gegevens](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics-agent voor Linux is niet juist geconfigureerd

De Log Analytics-agent voor Linux is mogelijk niet juist geconfigureerd voor de logboek-en opdracht regel uitvoer verzameling met behulp van het hulp programma OMS-logboek verzamelaar. Bekijk [wijzigingen in uw omgeving bijhouden met de wijzigingen bijhouden oplossing](../change-tracking.md).

##### <a name="fim-conflicts"></a>FIM-conflicten

De FIM-functie van Azure Security Center kan de integriteit van uw Linux-bestanden onjuist valideren. Controleer of FIM operationeel is en correct is geconfigureerd voor Linux-bestands bewaking. Bekijk [wijzigingen in uw omgeving bijhouden met de wijzigingen bijhouden oplossing](../change-tracking.md).

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, kunt u een van de volgende kanalen gebruiken voor meer ondersteuning.

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure-account voor het verbeteren van de gebruikerservaring door de Azure-community in contact te brengen met de juiste resources: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
