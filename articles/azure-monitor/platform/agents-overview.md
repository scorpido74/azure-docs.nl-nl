---
title: Overzicht van de Azure-bewakingsagents| Microsoft Documenten
description: In dit artikel vindt u een gedetailleerd overzicht van de beschikbare Azure-agents die ondersteuning bieden voor het bewaken van virtuele machines die worden gehost in Azure of een hybride omgeving.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249085"
---
# <a name="overview-of-azure-monitor-agents"></a>Overzicht van Azure Monitor-agents

Voor virtuele machines en andere rekenbronnen moet een agent bewakingsgegevens verzamelen om de prestaties en beschikbaarheid van hun gastbesturingssysteem en workloads te meten. In dit artikel worden de agents beschreven die door Azure Monitor worden gebruikt en u bepalen welke u aan de vereisten voor uw specifieke omgeving moet voldoen.

> [!NOTE]
> Azure Monitor heeft momenteel meerdere agents vanwege de recente consolidatie van Azure Monitor en Log Analytics. Hoewel er overlap kan zijn in hun functies, heeft elk unieke mogelijkheden. Afhankelijk van uw wensen heeft u mogelijk een of meer van de agents op uw virtuele machines nodig. 

Mogelijk hebt u een specifieke set vereisten waaraan niet volledig kan worden voldaan met één agent voor een bepaalde virtuele machine. U bijvoorbeeld metrische waarschuwingen gebruiken waarvoor azure-diagnostische extensie vereist is, maar ook de functionaliteit van Azure Monitor wilt gebruiken voor VM's waarvoor de log-analyse-agent en de afhankelijkheidsagent vereist zijn. In dergelijke gevallen u meerdere agents gebruiken en dit is een veelvoorkomend scenario voor klanten die functionaliteit van elk van hen nodig hebben.

## <a name="summary-of-agents"></a>Samenvatting van de agenten

De volgende tabellen bieden een snelle vergelijking van de Azure Monitor-agents voor Windows en Linux. Verdere details over elk is opgenomen in de sectie hieronder. 

### <a name="windows-agents"></a>Windows-agents

| | Diagnostiek<br>uitbreiding (WAD) | Log Analytics<br>Agent | Afhankelijkheid<br>Agent |
|:---|:---|:---|:---|
| Ondersteunde omgevingen | Azure | Azure<br>Andere cloud<br>On-premises | Azure<br>Andere cloud<br>On-premises | 
| Agentvereisten  | Geen | Geen | Vereist Log Analytics-agent |
| Verzamelde gegevens | Gebeurtenislogboeken<br>ETW-evenementen<br>Prestaties<br>Op bestanden gebaseerde logboeken<br>IIS-logboeken<br>.NET-applogboeken<br>Crashdumps<br>Agent diagnostische logboeken | Gebeurtenislogboeken<br>Prestaties<IIS logs><br>Op bestanden gebaseerde logboeken<br>Inzichten en oplossingen<br>Overige services | Procesdetails en afhankelijkheden<br>Statistieken voor netwerkverbindingen |
| Gegevens verzonden naar | Azure Storage<br>Azure-monitorstatistieken<br>Event Hub | Azure Monitor-logboeken | Azure Monitor-logboeken |


### <a name="linux-agents"></a>Linux-agents

| | Diagnostiek<br>uitbreiding (LAD) | Telegraf Telegraf<br>Agent | Log Analytics<br>Agent | Afhankelijkheid<br>Agent |
|:---|:---|:---|:---|:---|
| Ondersteunde omgevingen | Azure | Azure<br>Andere cloud<br>On-premises | Azure<br>Andere cloud<br>On-premises | Azure<br>Andere cloud<br>On-premises |
| Agentvereisten  | Geen | Geen | Geen | Vereist Log Analytics-agent |
| Verzamelde gegevens | Syslog<br>Prestaties | Prestaties | Syslog<br>Prestaties| Procesdetails en afhankelijkheden<br>Statistieken voor netwerkverbindingen |
| Gegevens verzonden naar | Azure Storage<br>Event Hub | Azure-monitorstatistieken | Azure Monitor-logboeken | Azure Monitor-logboeken |

## <a name="log-analytics-agent"></a>Log Analytics-agent

De [Log Analytics-agent](log-analytics-agent.md) verzamelt bewakingsgegevens van het gastbesturingssysteem en workloads van virtuele machines in Azure, andere cloudproviders en on-premises. Het verzamelt gegevens in een Log Analytics-werkruimte. De log-analyse-agent is dezelfde agent die wordt gebruikt door System Center Operations Manager en u computers met meerdere home-agenten tegelijk communiceren met uw beheergroep en Azure Monitor. Deze agent is ook vereist door bepaalde inzichten en oplossingen in Azure Monitor.


> [!NOTE]
> De Log Analytics-agent voor Windows wordt vaak aangeduid als Microsoft Monitoring Agent (MMA). De Log Analytics-agent voor Linux wordt vaak OMS-agent genoemd.



Gebruik de agent Log Analytics als u:

* Verzamel logboeken en prestatiegegevens van virtuele of fysieke machines buiten Azure. 
* Stuur gegevens naar een log analytics-werkruimte om te profiteren van functies die worden ondersteund door [Azure Monitor-logboeken,](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) zoals [logboekquery's.](../log-query/log-query-overview.md)
* Gebruik [Azure Monitor voor VM's](../insights/vminsights-overview.md) waarmee u uw virtuele machines op schaal controleren en hun processen en afhankelijkheden van andere resources en externe processen monitoren..  
* Beheer de beveiliging van uw virtuele machines met [Azure Security Center](../../security-center/security-center-intro.md) of Azure [Sentinel.](../../sentinel/overview.md)
* [Azure Automation Update-beheer,](../../automation/automation-update-management.md) [Azure Automation State Configuration](../../automation/automation-dsc-overview.md)of Azure Automation Change Tracking and [Inventory](../../automation/change-tracking.md) gebruiken om uitgebreid beheer van uw Azure VM's te leveren
* Gebruik verschillende [oplossingen](../monitor-reference.md#insights-and-core-solutions) om een bepaalde service of toepassing te controleren.

Beperkingen van de Log Analytics-agent zijn:

- Kan geen gegevens verzenden naar Azure Monitor Metrics, Azure Storage of Azure Event Hubs.

## <a name="azure-diagnostics-extension"></a>Azure-extensie voor diagnostische gegevens

De [Azure Diagnostics-extensie](diagnostics-extension-overview.md) verzamelt bewakingsgegevens van het gastbesturingssysteem en workloads van virtuele Azure-machines en andere rekenbronnen. Het verzamelt voornamelijk gegevens in Azure Storage, maar stelt u ook in staat om gegevenssinks te definiëren om ook gegevens naar andere bestemmingen te verzenden, zoals Azure Monitor Metrics en Azure Event Hubs.

Gebruik de diagnostische extensie Azure als u:

- Stuur gegevens naar Azure Storage voor archivering of om deze te analyseren met hulpprogramma's zoals [Azure Storage Explorer.](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
- Stuur gegevens naar [Azure Monitor Metrics](data-platform-metrics.md) om deze te analyseren met metrics [explorer](metrics-getting-started.md) en om te profiteren van functies zoals near real-time [metrische waarschuwingen](../../azure-monitor/platform/alerts-metric-overview.md) en [autoscale](autoscale-overview.md) (alleen Windows).
- Gegevens verzenden naar hulpprogramma's van derden met [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
- Verzamel [Opstartdiagnostiek](../../virtual-machines/troubleshooting/boot-diagnostics.md) om problemen met het opstarten van VM's te onderzoeken.

Beperkingen van azure diagnostische extensie zijn:

- Kan alleen worden gebruikt met Azure-bronnen.
- Beperkte mogelijkheid om gegevens naar Azure Monitor-logboeken te verzenden.

## <a name="telegraf-agent"></a>Telegraf agent

De [InfluxData Telegraf-agent](collect-custom-metrics-linux-telegraf.md) wordt gebruikt om prestatiegegevens van Linux-computers te verzamelen naar Azure Monitor Metrics.

Gebruik de Telegraf-agent als u:

* Stuur gegevens naar [Azure Monitor Metrics](data-platform-metrics.md) om deze te analyseren met metrics [explorer](metrics-getting-started.md) en om te profiteren van functies zoals near real-time [metrische waarschuwingen](../../azure-monitor/platform/alerts-metric-overview.md) en [autoscale](autoscale-overview.md) (alleen Linux). 



## <a name="dependency-agent"></a>Agent voor afhankelijkheden

De afhankelijkheidsagent verzamelt gedetecteerde gegevens over processen die worden uitgevoerd op de virtuele machine en externe procesafhankelijkheden. 

Gebruik de afhankelijkheidsagent als u het nodig hebt:

* Gebruik de [Azure-monitor voor VM's](../insights/vminsights-overview.md) of de [Servicemap-oplossing](../insights/service-map.md) met de kaartfunctie.

Houd rekening met het volgende bij het gebruik van de afhankelijkheidsagent:

- De afhankelijkheidsagent vereist dat de Log Analytics-agent op dezelfde virtuele machine wordt geïnstalleerd.
- Op Linux VM's moet de loganalyse-agent zijn geïnstalleerd vóór de Azure Diagnostic Extension.

## <a name="extensions-compared-to-agents"></a>Extensies in vergelijking met agenten

De Log Analytics-extensie voor [Windows](../../virtual-machines/extensions/oms-windows.md) en [Linux](../../virtual-machines/extensions/oms-linux.md) installeert de Log Analytics-agent op virtuele Azure-machines. De Azure Monitor Dependency-extensie voor [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) en [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) installeert de afhankelijkheidsagent op virtuele Azure-machines. Dit zijn dezelfde agenten hierboven beschreven, maar u ze beheren door middel [van virtuele machine-extensies](../../virtual-machines/extensions/overview.md). U moet extensies gebruiken om de agents waar mogelijk te installeren en te beheren.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over elk van de agenten vindt u op het volgende:

- [Overzicht van de Log Analytics-agent](log-analytics-agent.md)
- [Overzicht van Azure Diagnostics-extensie](diagnostics-extension-overview.md)
- [Verzamel aangepaste statistieken voor een Linux-VM met de InfluxData Telegraf-agent](collect-custom-metrics-linux-telegraf.md)
