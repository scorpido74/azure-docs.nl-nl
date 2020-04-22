---
title: Problemen oplossen met het bijhouden en inventaris van wijzigingen
description: Meer informatie over het oplossen en oplossen van problemen met de azure automation change tracking and inventory-oplossing.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 11c1fd05055922b07801c20d525d852d5360b069
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679350"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Problemen met het bijhouden en inventaris van wijzigingen oplossen

In dit artikel wordt beschreven hoe u problemen met het bijhouden en inventaris van wijzigingen oplossen.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](../automation-update-azure-modules.md)

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenario: Recordrecords voor het bijhouden en inventaris wijzigen worden niet weergegeven voor Windows-machines

#### <a name="issue"></a>Probleem

U ziet geen resultaten voor het bijhouden en inventaris van wijzigingen voor Windows-machines die aan boord zijn.

#### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

* De log-analyse-agent voor Windows wordt niet uitgevoerd.
* De communicatie naar het Automation-account wordt geblokkeerd.
* De pakketten Change Tracking en Inventory Management worden niet gedownload.
* De VM wordt aan boord kan afkomstig zijn van een gekloonde machine die niet was sysprepped met de Log Analytics agent voor Windows geïnstalleerd.

#### <a name="resolution"></a>Oplossing

Navigeer op de agentmachine logAnalytics naar **C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** en voer de volgende opdrachten uit:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Als u nog steeds hulp nodig hebt, u diagnostische informatie verzamelen en contact opnemen met ondersteuning. 

> [!NOTE]
> De agent Log Analyticss maakt standaard fouttracering mogelijk. Als u verbose-foutberichten wilt inschakelen zoals `VER` in het voorgaande voorbeeld, gebruikt u de parameter. Voor informatiesporen, `INF` gebruik `StartTracing.cmd`bij een beroep .

##### <a name="log-analytics-agent-for-windows-not-running"></a>Log Analytics-agent voor Windows wordt niet uitgevoerd

Controleer of de agent Log Analytics voor Windows **(HealthService.exe)** op de machine wordt uitgevoerd.

##### <a name="communication-to-automation-account-blocked"></a>Communicatie naar automatiseringsaccount geblokkeerd

Controleer Logboeken op de machine en zoek `changetracking` naar gebeurtenissen die het woord in zich hebben.

Zie [Resources automatiseren in uw datacenter of cloud met Hybride runbookworker](../automation-hybrid-runbook-worker.md#network-planning) om meer te weten te komen over adressen en poorten die moeten worden toegestaan om wijzigingen bij houden en voorraad te laten werken.

##### <a name="management-packs-not-downloaded"></a>Beheerpakketten niet gedownload

Controleer of de volgende pakketten voor het bijhouden en beheren van wijzigingen lokaal zijn geïnstalleerd:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM van gekloonde machine die niet is gesysprepped

Als u een gekloonde afbeelding gebruikt, moet u de afbeelding eerst voorbereiden en vervolgens de agent Log Analytics voor Windows installeren.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scenario: No Change Tracking and Inventory results on Linux machines Scenario: No Change Tracking and Inventory results on Linux machines Scenario: No Change Tracking and Inventory results on Linux machines Scenario:

#### <a name="issue"></a>Probleem

U ziet geen voorraad- en wijzigingstrackingresultaten voor Linux-machines die zijn aanboord voor de oplossing. 

#### <a name="cause"></a>Oorzaak
Hier zijn mogelijke oorzaken specifiek voor dit probleem:
* De Log Analytics-agent voor Linux wordt niet uitgevoerd.
* De Log Analytics-agent voor Linux is niet correct geconfigureerd.
* Er zijn FIM-conflicten (File Integrity Monitoring).

#### <a name="resolution"></a>Oplossing 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics-agent voor Linux wordt niet uitgevoerd

Controleer of de daemon voor de Log Analytics-agent voor Linux **(omsagent)** op uw machine wordt uitgevoerd. Voer de volgende query uit in de werkruimte Log Analytics die is gekoppeld aan uw Automatiseringsaccount.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Als u uw machine niet ziet in queryresultaten, is deze niet onlangs ingecheckt. Er is waarschijnlijk een lokaal configuratieprobleem en u moet de agent opnieuw installeren. Zie [Logboekgegevens verzamelen met de loganalyse-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)voor informatie over installatie en configuratie. 

Als uw machine wordt weergegeven in de queryresultaten, controleert u de scopeconfiguratie. Zie [Oplossingen voor targetingbewaking in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Zie [Probleem: U ziet geen Linux-gegevens voor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)meer problemen.

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics-agent voor Linux niet correct geconfigureerd

De loganalyse-agent voor Linux is mogelijk niet correct geconfigureerd voor het verzamelen van logboek- en opdrachtregeluitvoer met behulp van het gereedschap OMS-logboekverzamelaar. Zie [Wijzigingen in uw omgeving bijhouden met de oplossing Voor het bijhouden en inventaris van wijzigingen](../change-tracking.md).

##### <a name="fim-conflicts"></a>FIM-conflicten

De FIM-functie van Azure Security Center valideert mogelijk ten onrechte de integriteit van uw Linux-bestanden. Controleer of FIM operationeel en correct is geconfigureerd voor Linux-bestandsbewaking. Zie [Wijzigingen in uw omgeving bijhouden met de oplossing Voor het bijhouden en inventaris van wijzigingen](../change-tracking.md).

## <a name="next-steps"></a>Volgende stappen

Als je het probleem hierboven niet ziet of het probleem niet oplossen, probeer je een van de volgende kanalen voor extra ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport)verbinding met het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
