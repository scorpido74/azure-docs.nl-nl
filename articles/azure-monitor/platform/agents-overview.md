---
title: Overzicht van de Azure-bewakings agents | Microsoft Docs
description: Dit artikel bevat een gedetailleerd overzicht van de Azure-agents die beschikbaar zijn voor het bewaken van virtuele machines die worden gehost in azure of een hybride omgeving.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84686661"
---
# <a name="overview-of-azure-monitor-agents"></a>Overzicht van Azure Monitor agents

Voor virtuele machines en andere reken bronnen is een agent vereist om bewakings gegevens te verzamelen om de prestaties en beschik baarheid van hun gast besturingssysteem en werk belastingen te meten. In dit artikel worden de agents beschreven die door Azure Monitor worden gebruikt en kunt u bepalen wat u moet doen aan de vereisten voor uw specifieke omgeving.

> [!NOTE]
> Azure Monitor heeft momenteel meerdere agents vanwege een recente consolidatie van Azure Monitor en Log Analytics. Hoewel er mogelijk overlap pingen zijn in de functies, heeft elk een unieke capaciteit. Afhankelijk van uw vereisten hebt u mogelijk een of meer van de agents op uw virtuele machines nodig. 

Mogelijk hebt u een specifieke set vereisten waaraan niet volledig kan worden voldaan met één agent voor een bepaalde virtuele machine. U kunt bijvoorbeeld metrische waarschuwingen gebruiken waarvoor Azure Diagnostics-extensie vereist is, maar u ook wilt gebruikmaken van de functionaliteit van Azure Monitor voor VM's waarvoor de Log Analytics agent en de afhankelijkheids agent vereist zijn. In dergelijke gevallen kunt u meerdere agents gebruiken. Dit is een veelvoorkomend scenario voor klanten die van elke functionaliteit moeten zijn.

## <a name="summary-of-agents"></a>Samen vatting van agents

De volgende tabellen bieden een snelle vergelijking van de Azure Monitor agents voor Windows en Linux. Meer details hierover vindt u in de volgende sectie. 

### <a name="windows-agents"></a>Windows-agents

| | Diagnostiek<br>extensie (WAD) | Log Analytics<br>agent | Afhankelijkheid<br>agent |
|:---|:---|:---|:---|
| Omgevingen worden ondersteund | Azure | Azure<br>Andere Cloud<br>On-premises | Azure<br>Andere Cloud<br>On-premises | 
| Agent vereisten  | Geen | Geen | Vereist Log Analytics-agent |
| Verzamelde gegevens | Gebeurtenislogboeken<br>ETW-gebeurtenissen<br>Prestaties<br>Logboeken op basis van bestanden<br>IIS-logboeken<br>.NET-app-logboeken<br>Crashdumps<br>Logboeken met diagnostische gegevens van agent | Gebeurtenislogboeken<br>Prestaties<IIS logs><br>Logboeken op basis van bestanden<br>Inzicht en oplossingen<br>Overige services | Details en afhankelijkheden van het proces<br>Metrische netwerk verbindings gegevens |
| Gegevens die worden verzonden naar | Azure Storage<br>Azure Monitor metrische gegevens<br>Event Hub | Azure Monitor-logboeken | Azure Monitor-logboeken |


### <a name="linux-agents"></a>Linux-agents

| | Diagnostiek<br>extensie (LAD) | Telegrafie<br>agent | Log Analytics<br>agent | Afhankelijkheid<br>agent |
|:---|:---|:---|:---|:---|
| Omgevingen worden ondersteund | Azure | Azure<br>Andere Cloud<br>On-premises | Azure<br>Andere Cloud<br>On-premises | Azure<br>Andere Cloud<br>On-premises |
| Agent vereisten  | Geen | Geen | Geen | Vereist Log Analytics-agent |
| Verzamelde gegevens | Syslog<br>Prestaties | Prestaties | Syslog<br>Prestaties| Details en afhankelijkheden van het proces<br>Metrische netwerk verbindings gegevens |
| Gegevens die worden verzonden naar | Azure Storage<br>Event Hub | Azure Monitor metrische gegevens | Azure Monitor-logboeken | Azure Monitor-logboeken |

## <a name="log-analytics-agent"></a>Log Analytics-agent

De [log Analytics-agent](log-analytics-agent.md) verzamelt bewakings gegevens van het gast besturingssysteem en werk belastingen van virtuele machines in azure, andere cloud providers en on-premises. Hiermee worden gegevens verzameld in een Log Analytics-werk ruimte. De Log Analytics-agent is dezelfde agent die wordt gebruikt door System Center Operations Manager, en u kunt computers met meerdere thuis agenten gebruiken om met uw beheer groep en Azure Monitor tegelijk te communiceren. Deze agent is ook vereist voor bepaalde inzichten en oplossingen in Azure Monitor.


> [!NOTE]
> De Log Analytics-agent voor Windows wordt vaak micro soft Monitoring Agent (MMA) genoemd. De Log Analytics-agent voor Linux wordt vaak OMS-agent genoemd.



Gebruik de Log Analytics agent als u het volgende moet doen:

* Logboeken en prestatie gegevens verzamelen van virtuele of fysieke machines buiten Azure. 
* Gegevens verzenden naar een Log Analytics-werk ruimte om te profiteren van de functies die worden ondersteund door [Azure monitor-logboeken](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , zoals [logboek query's](../log-query/log-query-overview.md).
* Gebruik [Azure monitor voor VM's](../insights/vminsights-overview.md) waarmee u uw virtuele machines op schaal kunt bewaken en de processen en afhankelijkheden van andere bronnen en externe processen bewaakt.  
* Beheer de beveiliging van uw virtuele machines met behulp van [Azure Security Center](../../security-center/security-center-intro.md) of [Azure Sentinel](../../sentinel/overview.md).
* Gebruik [Azure Automation update beheer](../../automation/automation-update-management.md), [Azure Automation status configuratie](../../automation/automation-dsc-overview.md)of [Azure Automation wijzigingen bijhouden en inventaris](../../automation/change-tracking.md) om een uitgebreid beheer van uw Azure-vm's te leveren
* Gebruik verschillende [oplossingen](../monitor-reference.md#insights-and-core-solutions) voor het bewaken van een bepaalde service of toepassing.

De beperkingen van de Log Analytics-agent zijn:

- Kan geen gegevens verzenden naar Azure Monitor meet waarden, Azure Storage of Azure Event Hubs.

## <a name="azure-diagnostics-extension"></a>Azure-extensie voor diagnostische gegevens

Met de [extensie Azure Diagnostics](diagnostics-extension-overview.md) worden bewakings gegevens verzameld van het gast besturingssysteem en de workloads van Azure virtual machines en andere reken bronnen. Er worden voornamelijk gegevens in Azure Storage verzameld, maar u kunt ook gegevenssinks definiëren om gegevens te verzenden naar andere bestemmingen, zoals Azure Monitor metrieken en Azure Event Hubs.

Gebruik de diagnostische extensie van Azure als u het volgende moet doen:

- Gegevens verzenden naar Azure Storage voor archivering of het analyseren met hulpprogram ma's zoals [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Gegevens verzenden naar [Azure monitor metrieken](data-platform-metrics.md) om deze te analyseren met [metrische gegevens Verkenner](metrics-getting-started.md) en te profiteren van functies zoals bijna realtime [waarschuwingen](../../azure-monitor/platform/alerts-metric-overview.md) en [automatisch schalen](autoscale-overview.md) (alleen Windows).
- Gegevens verzenden naar hulpprogram ma's van derden met behulp van [Azure Event hubs](diagnostics-extension-stream-event-hubs.md).
- Verzamel de [Diagnostische gegevens over opstarten](../../virtual-machines/troubleshooting/boot-diagnostics.md) om opstart problemen met de virtuele machine te onderzoeken.

De beperkingen van de Azure Diagnostics-extensie zijn onder andere:

- Kan alleen worden gebruikt met Azure-resources.
- Beperkte mogelijkheid om gegevens te verzenden naar Azure Monitor-Logboeken.

## <a name="telegraf-agent"></a>Telegraf-agent

De [InfluxData-telegrafe-agent](collect-custom-metrics-linux-telegraf.md) wordt gebruikt voor het verzamelen van prestatie gegevens van Linux-computers tot Azure monitor meet waarden.

Gebruik telegrafie agent als u het volgende moet doen:

* Verzend gegevens naar [Azure monitor metrieken](data-platform-metrics.md) om deze te analyseren met [metrische gegevens Verkenner](metrics-getting-started.md) en gebruik te maken van functies zoals bijna realtime [waarschuwingen](../../azure-monitor/platform/alerts-metric-overview.md) en [automatisch schalen](autoscale-overview.md) (alleen Linux). 



## <a name="dependency-agent"></a>Agent voor afhankelijkheden

De afhankelijkheids agent verzamelt gedetecteerde gegevens over processen die worden uitgevoerd op de virtuele machine en de afhankelijkheden van het externe proces. 

Gebruik de afhankelijkheids agent als u het volgende moet doen:

* Gebruik de kaart functie [Azure monitor voor VM's](../insights/vminsights-overview.md) of de [servicetoewijzing](../insights/service-map.md) oplossing.

Houd rekening met het volgende wanneer u de afhankelijkheids agent gebruikt:

- Voor de afhankelijkheids agent moet de Log Analytics agent op dezelfde virtuele machine zijn geïnstalleerd.
- Op virtuele Linux-machines moet de Log Analytics-agent zijn geïnstalleerd vóór de diagnostische Azure-extensie.

## <a name="extensions-compared-to-agents"></a>Uitbrei dingen vergeleken met agents

De Log Analytics-extensie voor [Windows](../../virtual-machines/extensions/oms-windows.md) en [Linux](../../virtual-machines/extensions/oms-linux.md) Installeer de log Analytics agent op virtuele machines van Azure. Met de uitbrei ding voor de Azure Monitor afhankelijkheid voor [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) en [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) wordt de afhankelijkheids agent op virtuele machines van Azure geïnstalleerd. Dit zijn dezelfde agents die hierboven worden beschreven, maar u kunt ze ook beheren via de extensies van de [virtuele machine](../../virtual-machines/extensions/overview.md). Gebruik waar mogelijk extensies om de agents te installeren en te beheren.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over elk van de agents vindt u op het volgende:

- [Overzicht van de Log Analytics-agent](log-analytics-agent.md)
- [Overzicht van Azure Diagnostics extensie](diagnostics-extension-overview.md)
- [Aangepaste metrische gegevens verzamelen voor een virtuele Linux-machine met de InfluxData-Telegraf-agent](collect-custom-metrics-linux-telegraf.md)
