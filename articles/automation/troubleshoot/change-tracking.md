---
title: Problemen met Wijzigingen bijhouden en inventarisatie oplossen
description: Meer informatie over het oplossen van problemen met de Azure Automation Wijzigingen bijhouden en de voorraad oplossing.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 11c1fd05055922b07801c20d525d852d5360b069
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679350"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Problemen met Wijzigingen bijhouden-en inventaris problemen oplossen

In dit artikel wordt beschreven hoe u problemen met Wijzigingen bijhouden en inventaris kunt oplossen.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](../automation-update-azure-modules.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenario: Wijzigingen bijhouden-en inventaris records worden niet weer gegeven voor Windows-computers

#### <a name="issue"></a>Probleem

U ziet geen Wijzigingen bijhouden-en inventaris resultaten voor Windows-machines die worden uitgevoerd.

#### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

* De Log Analytics-agent voor Windows wordt niet uitgevoerd.
* De communicatie met het Automation-account wordt geblokkeerd.
* De Management Packs voor Wijzigingen bijhouden en inventarisatie zijn niet gedownload.
* De virtuele machine die wordt vrijgegeven, kan afkomstig zijn van een gekloonde computer die niet is Sysprep met de Log Analytics agent voor Windows is geïnstalleerd.

#### <a name="resolution"></a>Oplossing

Ga op de Log Analytics agent machine naar **C:\Program Files\Microsoft monitoring Agent\Agent\Tools** en voer de volgende opdrachten uit:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Als u nog steeds hulp nodig hebt, kunt u Diagnostische gegevens verzamelen en contact opnemen met ondersteuning. 

> [!NOTE]
> De log Analytics-agent schakelt standaard fout tracering in. Als u uitgebreide fout berichten wilt inschakelen, zoals in het voor gaande voor `VER` beeld, gebruikt u de para meter. Gebruik `INF` bij het aanroepen van informatie traceringen `StartTracing.cmd`.

##### <a name="log-analytics-agent-for-windows-not-running"></a>Log Analytics-agent voor Windows niet actief

Controleer of de Log Analytics-agent voor Windows (**HealthService. exe**) wordt uitgevoerd op de computer.

##### <a name="communication-to-automation-account-blocked"></a>Communicatie met Automation-account geblokkeerd

Controleer Logboeken op de computer en zoek naar gebeurtenissen die het woord `changetracking` erin bevatten.

Zie [resources automatiseren in uw Data Center of in de Cloud met behulp van Hybrid Runbook worker](../automation-hybrid-runbook-worker.md#network-planning) voor meer informatie over adressen en poorten die moeten worden toegestaan voor het werken met wijzigingen bijhouden en de inventaris.

##### <a name="management-packs-not-downloaded"></a>Management Packs niet gedownload

Controleer of de volgende Wijzigingen bijhouden-en Inventory Management Packs lokaal zijn geïnstalleerd:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM van een gekloonde computer die niet is Sysprep

Als u een gekloonde installatie kopie gebruikt, Sysprep de installatie kopie eerst en installeert u vervolgens de Log Analytics-agent voor Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scenario: geen Wijzigingen bijhouden-en inventaris resultaten op Linux-machines

#### <a name="issue"></a>Probleem

U ziet geen inventaris-en Wijzigingen bijhouden resultaten voor Linux-machines die zijn voor bereid voor de oplossing. 

#### <a name="cause"></a>Oorzaak
Hier vindt u mogelijke oorzaken die specifiek zijn voor dit probleem:
* De Log Analytics-agent voor Linux wordt niet uitgevoerd.
* De Log Analytics-agent voor Linux is niet juist geconfigureerd.
* Er zijn FIM-conflicten (File Integrity Monitoring).

#### <a name="resolution"></a>Oplossing 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics-agent voor Linux niet actief

Controleer of de daemon voor de Log Analytics-agent voor Linux (**omsagent**) wordt uitgevoerd op de computer. Voer de volgende query uit in de Log Analytics-werk ruimte die is gekoppeld aan uw Automation-account.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Als uw machine niet in query resultaten wordt weer geven, is deze niet recent ingecheckt. Er is waarschijnlijk een probleem met de lokale configuratie en u moet de agent opnieuw installeren. Zie [logboek gegevens verzamelen met de log Analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)voor meer informatie over de installatie en configuratie. 

Als uw computer in de query resultaten wordt weer gegeven, controleert u de scope configuratie. Zie [oplossingen voor doel bewaking in azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Zie [probleem: u ziet geen Linux-gegevens](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)voor meer probleem oplossing van dit probleem.

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics-agent voor Linux is niet juist geconfigureerd

De Log Analytics-agent voor Linux is mogelijk niet juist geconfigureerd voor de logboek-en opdracht regel uitvoer verzameling met behulp van het hulp programma OMS-logboek verzamelaar. Bekijk [wijzigingen in uw omgeving bijhouden met behulp van de oplossing wijzigingen bijhouden en inventarisatie](../change-tracking.md).

##### <a name="fim-conflicts"></a>FIM-conflicten

De FIM-functie van Azure Security Center kan de integriteit van uw Linux-bestanden onjuist valideren. Controleer of FIM operationeel is en correct is geconfigureerd voor Linux-bestands bewaking. Bekijk [wijzigingen in uw omgeving bijhouden met behulp van de oplossing wijzigingen bijhouden en inventarisatie](../change-tracking.md).

## <a name="next-steps"></a>Volgende stappen

Als uw probleem niet hierboven wordt weer geven of als u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport)met, het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
