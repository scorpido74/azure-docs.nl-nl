---
title: Overzicht van de Azure-bewakings agents | Microsoft Docs
description: Dit artikel bevat een gedetailleerd overzicht van de Azure-agents die beschikbaar zijn voor het bewaken van virtuele machines die worden gehost in azure of een hybride omgeving.
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: ae799e9a852b8700399ef695c54b3348174b560c
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069401"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Overzicht van de Azure Monitor agents 
Reken resources zoals virtuele machines genereren gegevens om de prestaties en beschik baarheid te controleren, net als [andere cloud resources](../insights/monitor-azure-resource.md). Reken resources hebben ook een gast besturingssysteem en werk belastingen die moeten worden bewaakt. Voor het verzamelen van deze bewakings gegevens in de resource is een agent vereist. In dit artikel worden de agents beschreven die door Azure Monitor worden gebruikt en kunt u bepalen wat u moet doen aan de vereisten voor uw specifieke omgeving.

## <a name="summary-of-agents"></a>Samen vatting van agents

> [!NOTE]
> Azure Monitor heeft momenteel meerdere agents vanwege een recente consolidatie van Azure Monitor en Log Analytics. Beide agents delen enkele mogelijkheden terwijl andere functies uniek zijn voor een bepaalde agent. Afhankelijk van uw vereisten hebt u mogelijk een van de agents of beide nodig. 

Azure Monitor heeft drie agents die elk een specifieke functionaliteit bieden. Afhankelijk van uw vereisten kunt u één agent of meerdere op uw virtuele machines en andere reken bronnen installeren.

* [Azure Diagnostics extensie](#azure-diagnostic-extension)
* [Log Analytics-agent](#log-analytics-agent)
* [Afhankelijkheids agent](#dependency-agent)

De volgende tabel bevat een snelle vergelijking van de verschillende agents. Raadpleeg de rest van dit artikel voor meer informatie.

| | Diagnostische Azure-extensie | Log Analytics-agent | Agent voor afhankelijkheden |
|:---|:---|:---|:---|
| Omgevingen worden ondersteund | Azure | Azure<br>Andere Cloud<br>On-premises | Azure<br>Andere Cloud<br>On-premises |
| Besturingssystemen | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| Agent afhankelijkheden  | None | None | Vereist Log Analytics-agent |
| Gegevens die zijn verzameld | Gebeurtenislogboeken<br>ETW-gebeurtenissen<br>Syslog<br>Prestaties<br>IIS-logboeken<br>Uitvoer logboeken voor .NET-app-tracering<br>Crashdumps | Gebeurtenislogboeken<br>Syslog<br>Prestaties<br>IIS-logboeken<br>Aangepaste logboeken<br>Gegevens van oplossingen | Details en afhankelijkheden van het proces<br>Metrische netwerk verbindings gegevens |
| Gegevens die worden verzonden naar | Azure Storage<br>Azure Monitor metrische gegevens<br>Event Hub | Azure Monitor-logboeken | Azure Monitor-logboeken |



## <a name="azure-diagnostic-extension"></a>Diagnostische Azure-extensie
De [uitbrei ding](../../azure-monitor/platform/diagnostics-extension-overview.md) van de Azure Diagnostics verzamelt bewakings gegevens van het gast besturingssysteem en werk belastingen van Azure Compute-resources. Het verzamelt voornamelijk gegevens in Azure Storage. U kunt Azure Monitor zo configureren dat de gegevens van de opslag naar een Log Analytics werkruimte worden gekopieerd. U kunt ook prestatie gegevens van de gast verzamelen in Azure Monitor metrieken.

De diagnostische extensie van Azure wordt vaak de uitbrei ding Windows Azure Diagnostic (WAD) of Linux Azure Diagnostic (LAD) genoemd.


### <a name="scenarios-supported"></a>Scenario's die worden ondersteund

De volgende scenario's worden ondersteund door de uitbrei ding van de Azure Diagnostics:

* Logboeken en prestatie gegevens verzamelen van Azure Compute-resources.
* Archiveer logboeken en prestatie gegevens van het gast besturingssysteem naar Azure Storage.
* Bewakings gegevens in de opslag weer geven met een hulp programma zoals [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
* Verzamelen van prestatie gegevens in een Data Base met metrieken om te profiteren van de functies die worden ondersteund door [Azure monitor metrische gegevens](data-platform-metrics.md) , zoals bijna realtime [waarschuwingen](../../azure-monitor/platform/alerts-metric-overview.md) en [automatisch schalen](autoscale-overview.md). 
* Verzamel bewakings gegevens van [de opslag naar een log Analytics-werk ruimte](azure-storage-iis-table.md) om te profiteren van de functies die door [Azure monitor logboeken](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) worden ondersteund, zoals [logboek query's](../log-query/log-query-overview.md).
* Verzend bewakings gegevens naar hulpprogram ma's van derden met behulp van [Azure Event hubs](diagnostics-extension-stream-event-hubs.md).
* Onderzoek de opstart problemen met de virtuele machine met [Diagnostische gegevens over opstarten](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Kopieer gegevens van toepassingen die worden uitgevoerd in uw virtuele machine [naar Application Insights](diagnostics-extension-to-application-insights.md) om te integreren met andere toepassings bewaking.

## <a name="log-analytics-agent"></a>Log Analytics-agent
De [log Analytics-agent](log-analytics-agent.md) verzamelt bewakings gegevens van het gast besturingssysteem en werk belastingen van virtuele machines in azure, andere cloud providers en on-premises. Hiermee worden gegevens verzameld in een Log Analytics-werk ruimte.

De Log Analytics-agent is dezelfde agent die wordt gebruikt door System Center Operations Manager en de multihome agent computers om te communiceren met uw beheer groep en Azure Monitor tegelijk. Deze agent is ook vereist voor bepaalde oplossingen in Azure Monitor.

De Log Analytics-agent voor Windows wordt vaak aangeduid als micro soft Management Agent (MMA). De Log Analytics-agent voor Linux wordt vaak OMS-agent genoemd.


### <a name="scenarios-supported"></a>Scenario's die worden ondersteund

De volgende scenario's worden ondersteund door de Log Analytics-agent:

* Verzamelen van Logboeken en prestatie gegevens van virtuele machines in azure, andere cloud providers en on-premises. 
* Verzamel bewakings gegevens in een Log Analytics-werk ruimte om te profiteren van de functies die worden ondersteund door [Azure monitor logboeken](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , zoals [logboek query's](../log-query/log-query-overview.md).
* Gebruik Azure Monitor bewakings oplossingen zoals [Azure monitor voor VM's](../insights/vminsights-overview.md), [Azure monitor voor containers](../insights/container-insights-overview.md), enzovoort.  
* De beveiliging van uw virtuele machines beheren met [Azure Sentinel](../../sentinel/overview.md) waarvoor de log Analytics-agent is vereist.
* De beveiliging van uw virtuele machines beheren met [Azure Security Center](../../security-center/security-center-intro.md) waarvoor de log Analytics-agent is vereist.
* Gebruik functies van [Azure Automation](../../automation/automation-intro.md) om uw Azure-vm's uitgebreid te beheren via hun levens cyclus.  Voor beelden van deze functies waarvoor de Log Analytics-agent is vereist zijn:
  * [Azure Automation update beheer](../../automation/automation-update-management.md) van updates van het besturings systeem.
  * [Azure Automation status configuratie](../../automation/automation-dsc-overview.md) om een consistente configuratie status te behouden.
  * Wijzigingen in de configuratie bijhouden met [Azure Automation wijzigingen bijhouden en de inventarisatie](../../automation/change-tracking.md).

## <a name="dependency-agent"></a>Agent voor afhankelijkheden
De afhankelijkheids agent verzamelt gedetecteerde gegevens over processen die worden uitgevoerd op de virtuele machine en de afhankelijkheden van het externe proces. Deze agent is vereist voor [servicetoewijzing](../insights/service-map.md) en de kaart functie [Azure monitor voor VM's](../insights/vminsights-overview.md). De agent voor afhankelijkheden vereist de Log Analytics agent en schrijft gegevens naar een Log Analytics-werk ruimte in Azure Monitor.


## <a name="using-multiple-agents"></a>Meerdere agents gebruiken
Mogelijk hebt u specifieke vereisten voor het gebruik van de diagnostische Azure-extensie of de Log Analytics-agent voor een bepaalde virtuele machine. U kunt bijvoorbeeld metrische waarschuwingen gebruiken waarvoor de diagnostische Azure-extensie is vereist. Maar mogelijk wilt u ook de kaart functie van Azure Monitor voor VM's gebruiken waarvoor de afhankelijkheids agent en de Log Analytics-agent zijn vereist. In dit geval kunt u meerdere agents gebruiken. Dit is een veelvoorkomend scenario voor klanten die van elke functionaliteit moeten zijn.

### <a name="considerations"></a>Overwegingen

- Voor de afhankelijkheids agent moet de Log Analytics agent op dezelfde virtuele machine zijn geïnstalleerd.
- Op virtuele Linux-machines moet de Log Analytics-agent zijn geïnstalleerd vóór de diagnostische Azure-extensie.


## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van de log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md) om vereisten en ondersteunde methoden te bekijken voor het implementeren van de agent op computers die worden gehost in azure, in uw Data Center of in een andere cloud omgeving.

