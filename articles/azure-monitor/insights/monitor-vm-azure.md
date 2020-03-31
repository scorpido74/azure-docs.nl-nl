---
title: Azure-virtuele machines bewaken met Azure Monitor
description: Beschrijft hoe u bewakingsgegevens van virtuele machines in Azure verzamelen en analyseren met Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283936"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Azure-virtuele machines bewaken met Azure Monitor
In dit artikel wordt beschreven hoe u Azure Monitor gebruiken om bewakingsgegevens van virtuele Azure-machines te verzamelen en te analyseren om hun status te behouden. Virtuele machines kunnen worden gecontroleerd op beschikbaarheid en prestaties met Azure Monitor, net als elke [andere Azure-bron,](monitor-azure-resource.md)maar ze zijn uniek uit andere bronnen, omdat u ook het gastbesturingssysteem en het gastsysteem en de workloads die erin worden uitgevoerd, moet controleren. 

> [!NOTE]
> In dit artikel vindt u een volledig overzicht van de concepten en opties voor het bewaken van virtuele machines in Azure Monitor. Zie [Quickstart: Monitor een virtuele azure-machine met Azure Monitor](../learn/quick-monitor-azure-vm.md)om snel te beginnen met het bewaken van uw virtuele machines zonder zich te concentreren op de onderliggende concepten.


## <a name="differences-from-other-azure-resources"></a>Verschillen met andere Azure-bronnen
Met het bewaken van [Azure-resources met Azure Monitor](monitor-azure-resource.md) worden de bewakingsgegevens beschreven die zijn gegenereerd door Azure-bronnen en hoe u de functies van Azure Monitor gebruiken om deze gegevens te analyseren en te waarschuwen. U dezelfde bewakingsgegevens van virtuele Azure-machines verzamelen en ernaar handelen met de volgende verschillen:

- [Platformstatistieken](../platform/data-platform-metrics.md) worden automatisch verzameld voor virtuele machines, maar alleen voor de [virtuele machinehost.](#monitoring-data) U hebt een agent nodig om prestatiegegevens van het gastbesturingssysteem te verzamelen. 
- Virtuele machines genereren geen [bronlogboeken](../platform/platform-logs-overview.md) die inzicht geven in bewerkingen die zijn uitgevoerd binnen een Azure-bron. U gebruikt een agent om logboekgegevens van het gastbesturingssysteem te verzamelen.
- U [diagnostische instellingen](../platform/diagnostic-settings.md) voor een virtuele machine maken om platformstatistieken naar andere bestemmingen te verzenden, zoals opslag- en gebeurtenishubs, maar u deze diagnostische instellingen niet configureren in de Azure-portal. 

## <a name="monitoring-data"></a>Bewakingsgegevens
Virtuele machines in Azure in Azure genereren [logboeken](../platform/data-platform-logs.md) en [metrische gegevens](../platform/data-platform-metrics.md) met het volgende diagram.

![Overzicht](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Virtuele machinehost
Virtuele machines in Azure genereren de volgende gegevens voor de host van de virtuele machine, hetzelfde als andere Azure-resources zoals beschreven in [bewakingsgegevens.](monitor-azure-resource.md#monitoring-data)

- [Platformstatistieken](../platform/data-platform-metrics.md) - Numerieke waarden die automatisch op regelmatige tijdstippen worden verzameld en een bepaald aspect van een resource op een bepaald moment beschrijven. Platformstatistieken worden verzameld voor de virtuele machinehost, maar u hebt de diagnostische extensie nodig om statistieken voor het gastbesturingssysteem te verzamelen.
- [Activiteitenlogboek](../platform/platform-logs-overview.md) - Biedt inzicht in de bewerkingen op elke Azure-bron in het abonnement van buitenaf (het beheervlak). Voor een virtuele machine omvat dit informatie zoals wanneer deze is gestart en eventuele configuratiewijzigingen.


### <a name="guest-operating-system"></a>Gastbesturingssysteem
Als u gegevens wilt verzamelen van het gastbesturingssysteem van een virtuele machine, hebt u een agent nodig die lokaal op elke virtuele machine wordt uitgevoerd en gegevens naar Azure Monitor verzendt. Er zijn meerdere agents beschikbaar voor Azure Monitor, waarbij elk verschillende gegevens verzamelt en gegevens naar verschillende locaties worden geschreven. Bekijk een gedetailleerde vergelijking van de verschillende agents bij [Overzicht van de Azure Monitor-agents.](../platform/agents-overview.md) 

- [Log Analytics-agent](../platform/agents-overview.md#log-analytics-agent) - Beschikbaar voor virtuele machines in Azure, andere cloudomgevingen en on-premises. Verzamelt gegevens naar Azure Monitor-logboeken. Ondersteunt Azure Monitor voor VM's en bewakingsoplossingen. Dit is dezelfde agent die wordt gebruikt voor System Center Operations Manager.
- [Afhankelijkheidsagent](../platform/agents-overview.md#dependency-agent) - Verzamelt gegevens over de processen die op de virtuele machine worden uitgevoerd en hun afhankelijkheden. Vertrouwt op de Log Analytics-agent om gegevens naar Azure te verzenden en ondersteunt Azure Monitor voor VM's, ServiceMap en Wire Data 2.0-oplossingen.
- [Azure Diagnostic-extensie](../platform/agents-overview.md#azure-diagnostics-extension) - Alleen beschikbaar voor virtuele azure-machines voor Azure Monitor. Kan gegevens verzamelen op meerdere locaties, maar voornamelijk worden gebruikt om prestatiegegevens van gasten te verzamelen in Azure Monitor Metrics voor virtuele Windows-machines.
- [Telegraf-agent](../platform/collect-custom-metrics-linux-telegraf.md) - Verzamel prestatiegegevens van Linux VM's in Azure Monitor Metrics.


## <a name="configuration-requirements"></a>Configuratievereisten
Als u alle functies van Azure Monitor wilt inschakelen voor het bewaken van een virtuele machine, moet u bewakingsgegevens van de virtuele machinehost en het gastbesturingssysteem verzamelen voor zowel [Azure Monitor Metrics](../platform/data-platform-logs.md) als Azure Monitor [Logs.](../platform/data-platform-logs.md) In de volgende tabel wordt de configuratie weergegeven die moet worden uitgevoerd om deze verzameling in te schakelen. U ervoor kiezen om niet al deze stappen uit te voeren, afhankelijk van uw specifieke vereisten.

| Configuratiestap | Acties voltooid | Functies ingeschakeld |
|:---|:---|:---|
| Geen configuratie | - Statistieken van het hostplatform verzameld om statistieken.<br>- Activiteitenlogboek verzameld. | - Metrics explorer voor host.<br>- Statistieken waarschuwingen voor host.<br>- Waarschuwingen voor activiteitenlogboeken. |
| [Azure-monitor voor VM's inschakelen](#enable-azure-monitor-for-vms) | - Log Analytics-agent geïnstalleerd.<br>- Afhankelijkheidsagent geïnstalleerd.<br>- Gastprestatiegegevens verzameld in logboeken.<br>- Proces- en afhankelijkheidsgegevens verzameld in logboeken. | - Prestatiegrafieken en werkmappen voor prestatiegegevens van gasten.<br>- Log query's voor gastprestatiegegevens.<br>- Log waarschuwingen voor gastprestatiegegevens.<br>- Afhankelijkheidskaart. |
| [De diagnostische extensie en telegraf-agent installeren](#enable-diagnostics-extension-and-telegraf-agent) | - Prestatiegegevens van gasten verzameld aan Statistieken. | - Metrics explorer voor gasten.<br>- Statistieken waarschuwingen voor gast.  |
| [Werkruimte Logboekanalyse configureren](#configure-log-analytics-workspace) | - Evenementen verzameld van gast. | - Log query's voor gastevenementen.<br>- Log waarschuwingen voor gastevenementen. |
| [Diagnostische instelling voor virtuele machine maken](#collect-platform-metrics-and-activity-log) | - Platformstatistieken verzameld voor logboeken.<br>- Activiteitenlogboek verzameld om logs. | - Loq-query's voor hoststatistieken.<br>- Log waarschuwingen voor host metrics.<br>- Log query's voor Activiteit log.

Elk van deze configuratiestappen wordt beschreven in de volgende secties.

### <a name="enable-azure-monitor-for-vms"></a>Azure-monitor voor VM's inschakelen
[Azure Monitor voor VM's](vminsights-overview.md) is een [inzicht](insights-overview.md) in Azure Monitor, het primaire hulpmiddel voor het bewaken van virtuele machines in Azure Monitor. Het biedt de volgende extra waarde ten opzichte van standaard Azure Monitor-functies.

- Vereenvoudigde onboarding van Log Analytics-agent en afhankelijkheidsagent om het bewaken van een gastbesturingssysteem en workloads van een virtuele machine gast mogelijk te maken. 
- Vooraf gedefinieerde trending performance charts en werkmappen waarmee u de belangrijkste prestatiestatistieken van het gastbesturingssysteem van de virtuele machine analyseren.
- Afhankelijkheidskaart die processen weergeeft die op elke virtuele machine en de onderling verbonden componenten met andere machines en externe bronnen worden uitgevoerd.

![Azure Monitor voor virtuele machines](media/monitor-vm-azure/vminsights-01.png)

![Azure Monitor voor virtuele machines](media/monitor-vm-azure/vminsights-02.png)


Azure Monitor voor VM's inschakelen vanuit de optie **Insights** in het menu van de virtuele machine van de Azure-portal. Zie [Overzicht van Azure Monitor voor VM's inschakelen](vminsights-enable-overview.md) voor meer informatie en andere configuratiemethoden.

![Azure-monitor voor VM's inschakelen](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Werkruimte Logboekanalyse configureren
De loganalyse-agent die wordt gebruikt door Azure Monitor voor VM's, verzendt gegevens naar een [loganalytics-werkruimte.](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured) U het verzamelen van aanvullende prestatiegegevens, gebeurtenissen en andere bewakingsgegevens van de agent inschakelen door de werkruimte Log Analytics te configureren. Het hoeft slechts één keer te worden geconfigureerd, omdat elke agent die verbinding maakt met de werkruimte automatisch de configuratie downloadt en onmiddellijk begint met het verzamelen van de gedefinieerde gegevens. 

U hebt rechtstreeks vanuit Azure Monitor voor VM's toegang tot de configuratie voor de werkruimte via Azure Monitor door **de configuratie van werkruimte** te selecteren in de **aandeslag.** Klik op de naam van de werkruimte om het menu te openen.

![Werkruimteconfiguratie](media/monitor-vm-azure/workspace-configuration.png)

Selecteer **Geavanceerde instellingen** in het menu van de werkruimte en vervolgens **Gegevens** om gegevensbronnen te configureren. Selecteer voor **Windows-agents Windows-gebeurtenislogboeken** en voeg algemene gebeurtenislogboeken toe, zoals *Systeem* en *Toepassing.* Selecteer voor Linux-agents **Syslog** en voeg algemene faciliteiten toe, zoals *kern* en *daemon.* Zie [Agent-gegevensbronnen in Azure Monitor](../platform/agent-data-sources.md) voor een lijst met de beschikbare gegevensbronnen en details over het configureren ervan. 

![Gebeurtenissen configureren](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> U prestatiemeteritems configureren die moeten worden verzameld uit de werkruimteconfiguratie, maar dit kan overbodig zijn met de prestatiemeteritems die door Azure Monitor voor VM's zijn verzameld. Azure Monitor voor VM's verzamelt de meest voorkomende set tellers met een frequentie van één keer per minuut. Configureer alleen prestatiemeteritems die door de werkruimte moeten worden verzameld als u tellers wilt verzamelen die nog niet door Azure Monitor zijn verzameld voor VM's of als u bestaande query's hebt met prestatiegegevens.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Diagnostische extensie en Telegraf-agent inschakelen
Azure Monitor voor VM's is gebaseerd op de Log Analytics-agent die gegevens verzamelt in een Log Analytics-werkruimte. Dit ondersteunt [meerdere functies van Azure Monitor,](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) zoals [logboekquery's,](../log-query/log-query-overview.md) [logboekwaarschuwingen](../platform/alerts-log.md)en [werkmappen](../platform/workbooks-overview.md). De [diagnostische extensie](../platform/diagnostics-extension-overview.md) verzamelt prestatiegegevens van het gastbesturingssysteem van virtuele Windows-machines naar Azure Storage en verzendt optioneel prestatiegegevens naar Azure Monitor [Metrics](../platform/data-platform-metrics.md). Voor virtuele Linux-machines is de [Telegraf-agent](../platform/collect-custom-metrics-linux-telegraf.md) vereist om gegevens naar Azure Metrics te verzenden.  Hiermee kunnen andere functies van Azure Monitor worden gebruikt, zoals [metrische gegevensverkenners](../platform/metrics-getting-started.md) en [metrische waarschuwingen.](../platform/alerts-metric.md) U de diagnostische extensie ook configureren om gebeurtenissen en prestatiegegevens buiten Azure Monitor te verzenden met Azure Event Hubs.

Installeer de diagnostische extensie voor één virtuele Windows-machine in de Azure-portal vanuit de optie **Diagnostische instelling** in het vm-menu. Selecteer de optie om **Azure Monitor** in te schakelen op het tabblad **Sinks.** Zie [Installatie en configuratie](../platform/diagnostics-extension-overview.md#installation-and-configuration)als u de extensie wilt inschakelen vanaf een sjabloon of opdrachtregel voor meerdere virtuele machines. In tegenstelling tot de Log Analytics-agent worden de gegevens die moeten worden verzameld gedefinieerd in de configuratie voor de extensie op elke virtuele machine.

![Diagnostische instelling](media/monitor-vm-azure/diagnostic-setting.png)

Zie [Telegraf installeren en configureren](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) voor meer informatie over het configureren van de Telegraf-agents op virtuele Linux-machines. De **menuoptie Diagnostische instelling** is beschikbaar voor Linux, maar u alleen gegevens naar Azure-opslag verzenden.

### <a name="collect-platform-metrics-and-activity-log"></a>Platformstatistieken en activiteitenlogboek verzamelen
U de platformstatistieken en het activiteitenlogboek bekijken dat is verzameld voor elke virtuele machinehost in de Azure-portal. Verzamel deze gegevens in dezelfde Log Analytics-werkruimte als Azure Monitor voor VM's om deze te analyseren met de andere bewakingsgegevens die voor de virtuele machine zijn verzameld. Deze verzameling is geconfigureerd met een [diagnostische instelling](../platform/diagnostic-settings.md). Verzamel het logboek Activiteit met een [diagnostische instelling voor het abonnement](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal).

Verzamel platformstatistieken met een diagnostische instelling voor de virtuele machine. In tegenstelling tot andere Azure-resources u geen diagnostische instelling maken voor een virtuele machine in de Azure-portal, maar moet u [een andere methode](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell)gebruiken. In de volgende voorbeelden ziet u hoe u statistieken voor een virtuele machine verzamelt met behulp van zowel PowerShell als CLI.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace

```

## <a name="monitoring-in-the-azure-portal"></a>Controle in de Azure-portal 
Zodra u het verzamelen van bewakingsgegevens voor een virtuele machine hebt geconfigureerd, hebt u meerdere opties om toegang te krijgen tot de installatie in de Azure-portal:

- Gebruik het menu **Azure Monitor** om toegang te krijgen tot gegevens van alle bewaakte bronnen. 
- Gebruik Azure Monitor voor VM's voor het bewaken van sets virtuele machines op schaal.
- Analyseer gegevens voor één virtuele machine in het menu in de Azure-portal. In de onderstaande tabel worden verschillende opties weergegeven voor het bewaken van het menu virtuele machines.

![Controle in de Azure-portal](media/monitor-vm-azure/monitor-menu.png)

| Menuoptie | Beschrijving |
|:---|:---|
| Overzicht | Hiermee worden [platformstatistieken](../platform/data-platform-metrics.md) voor de virtuele machinehost weergegeven. Klik op een grafiek om met deze gegevens te werken in [metrics explorer](../platform/metrics-getting-started.md). |
| Activiteitenlogboek | [Activiteitslogboekvermeldingen](../platform/activity-log-view.md) gefilterd voor de huidige virtuele machine. |
| Inzichten | Hiermee opent [u Azure Monitor voor VM's](../insights/vminsights-overview.md) met de kaart voor de huidige virtuele machine geselecteerd. |
| Waarschuwingen | Ziet [waarschuwingen](../platform/alerts-overview.md) voor de huidige virtuele machine.  |
| Metrische gegevens | Open [metrics explorer](../platform/metrics-getting-started.md) met het bereik ingesteld op de huidige virtuele machine. |
| Diagnostische instellingen | Diagnostische [extensie](../platform/diagnostics-extension-overview.md) voor de huidige virtuele machine inschakelen en configureren. |
| Advisor-aanbevelingen | Aanbevelingen voor de huidige virtuele machine van [Azure Advisor](/azure/advisor/). |
| Logboeken | Open [Log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) met het [bereik](../log-query/scope.md) ingesteld op de huidige virtuele machine. |
| Verbindingsmonitor | Open [Network Watcher Connection Monitor](../../network-watcher/connection-monitor-preview.md) om verbindingen tussen de huidige virtuele machine en andere virtuele machines te bewaken. |


## <a name="analyzing-metric-data"></a>Metrische gegevens analyseren
U statistieken voor virtuele machines analyseren met behulp van metrics explorer door **Statistieken** te openen in het menu van de virtuele machine. Zie [Aan de slag met Azure Metrics Explorer](../platform/metrics-getting-started.md) voor meer informatie over het gebruik van deze tool. 

Er zijn twee naamruimten die door virtuele machines worden gebruikt voor statistieken:

| Naamruimte | Beschrijving |
|:---|:---|
| Host van virtuele machine | Hoststatistieken die automatisch worden verzameld voor alle virtuele Azure-machines. Gedetailleerde lijst met statistieken bij [Microsoft.Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). |
| Virtuele machine gast | Statistieken van gastbesturingssystemen die zijn verzameld bij virtuele machines en diagnostische extensie zijn geïnstalleerd en geconfigureerd om naar Azure Monitor sink te verzenden. |

![Metrische gegevens](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Loggegevens analyseren
Azure virtual machines verzamelen de volgende gegevens in Azure Monitor Logs. 

Azure Monitor voor VM's maakt het mogelijk om een vooraf bepaalde set prestatiemeteritems te verzamelen die naar de tabel *InsightsMetrics* zijn geschreven. Dit is dezelfde tabel die wordt gebruikt door [Azure Monitor voor containers.](container-insights-overview.md) 

| Gegevensbron | Vereisten | Tabellen |
|:---|:---|:---|
| Azure Monitor voor virtuele machines | Inschakelen op elke virtuele machine. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VM-proces<br>Zie [Logboeken van Azure Monitor voor VM's](vminsights-log-search.md) voor meer informatie opvragen. |
| Activiteitenlogboek | Diagnostische instelling voor het abonnement. | AzureActivity |
| Hoststatistieken | Diagnostische instelling voor de virtuele machine. | AzureMetrics |
| Gegevensbronnen van het gastbesturingssysteem | Schakel de medewerker Log Analytics in en configureer gegevensbronnen. | Zie documentatie voor elke gegevensbron. |


> [!NOTE]
> Prestatiegegevens die door de log analytics-agent worden verzameld, worden naar de *perf-tabel* geschreven, terwijl Azure Monitor voor VM's deze verzamelt in de tabel *InsightsMetrics.* Dit zijn dezelfde gegevens, maar de tabellen hebben een andere structuur. Als u bestaande query's op basis van *Perf*hebt, moet deze worden herschreven om *InsightsMetrics*te kunnen gebruiken.


## <a name="alerts"></a>Waarschuwingen
[Waarschuwingen](../platform/alerts-overview.md) in Azure Monitor stellen u proactief op de hoogte wanneer belangrijke voorwaarden worden gevonden in uw bewakingsgegevens en starten mogelijk een actie, zoals het starten van een Logic App of het aanroepen van een webhook. Waarschuwingsregels definiëren de logica die wordt gebruikt om te bepalen wanneer een waarschuwing moet worden gemaakt. Azure Monitor verzamelt de gegevens die worden gebruikt door waarschuwingsregels, maar u moet regels maken om waarschuwingsvoorwaarden in uw Azure-abonnement te definiëren.

In de volgende secties worden de typen waarschuwingsregels en aanbevelingen beschreven over wanneer u elk moet gebruiken. Deze aanbeveling is gebaseerd op de functionaliteit en kosten van het type waarschuwingsregel. Zie [Azure Monitor-prijzen](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie over de prijzen van waarschuwingen.


### <a name="activity-log-alert-rules"></a>Waarschuwingsregels voor activiteitenlogboek
[Regels voor activiteitslogboekwaarschuwingen](../platform/alerts-activity-log.md) worden afgestoken wanneer een vermelding die overeenkomt met bepaalde criteria in het activiteitenlogboek. Ze hebben geen kosten, dus ze moeten uw eerste keuze zijn als de logica die u nodig hebt in het activiteitenlogboek staat. 

De doelbron voor waarschuwingen voor activiteitenlogboeken kan een specifieke virtuele machine zijn, alle virtuele machines in een resourcegroep of alle virtuele machines in een abonnement.

Maak bijvoorbeeld een waarschuwing als een kritieke virtuele machine wordt gestopt door de *virtuele machine uitschakelen* voor de signaalnaam te selecteren.

![Waarschuwing voor activiteitenlogboek](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Regels voor metrische waarschuwingen
[Metrische waarschuwingsregels](../platform/alerts-metric.md) worden afvuurd wanneer een metrische waarde een drempelwaarde overschrijdt. U een specifieke drempelwaarde definiëren of Azure Monitor toestaan om dynamisch een drempelwaarde te bepalen op basis van historische gegevens.  Gebruik metrische waarschuwingen waar mogelijk met metrische gegevens, omdat ze minder kosten en responsiever zijn dan waarschuwingsregels voor logboeken. Ze zijn ook stateful betekenis zullen ze zelf oplossen wanneer de metrische daalt onder de drempel.

De doelbron voor waarschuwingen voor activiteitenlogboeken kan een specifieke virtuele machine zijn of alle virtuele machines in een resourcegroep.

Als u bijvoorbeeld een waarschuwing wilt maken wanneer de processor van een virtuele machine een bepaalde waarde overschrijdt, maakt u een metrische waarschuwingsregel met *Percentage-CPU* als signaaltype. Stel een specifieke drempelwaarde in of stel Azure Monitor in om een dynamische drempelwaarde in te stellen. 

![Metrische waarschuwing](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Waarschuwingen voor logboeken
[Logboekalarmregels](../platform/alerts-log.md) worden afgestoken wanneer de resultaten van een geplande logboekquery overeenkomen met bepaalde criteria. Logboekquerywaarschuwingen zijn de duurste en minst responsieve waarschuwingsregels, maar ze hebben toegang tot de meest uiteenlopende gegevens en kunnen complexe logica uitvoeren die niet kan worden uitgevoerd door de andere waarschuwingsregels. 

De doelbron voor een logboekquery is een werkruimte loganalytics. Filter voor specifieke computers in de query.

Als u bijvoorbeeld een waarschuwing wilt maken die controleert of virtuele machines in een bepaalde resourcegroep offline zijn, gebruikt u de volgende query die een record retourneert voor elke computer die in de laatste tien minuten een hartslag heeft gemist. Gebruik een drempelwaarde van 1 die wordt geactiveerd als ten minste één computer een gemiste hartslag heeft.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Logboekwaarschuwing](media/monitor-vm-azure/log-alert-01.png)

Als u een waarschuwing wilt maken als er een buitensporig aantal mislukte aanmeldingen is opgetreden op virtuele Windows-apparaten in het abonnement, gebruikt u de volgende query waarmee een record wordt geretourneerd voor elke mislukte aanmeldingsgebeurtenis in het afgelopen uur. Gebruik een drempelwaarde die is ingesteld op het aantal mislukte aanmeldingen dat u toestaat. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Logboekwaarschuwing](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager (SCOM) biedt gedetailleerde bewaking van workloads op virtuele machines. Zie de [Cloud Monitoring Guide](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/) voor een vergelijking van monitoringplatforms en verschillende implementatiestrategieën.

Als u een bestaande SCOM-omgeving hebt die u wilt blijven gebruiken, u deze integreren met Azure Monitor om extra functionaliteit te bieden. De Log Analytics-agent die door Azure Monitor wordt gebruikt, wordt voor SCOM gebruikt, zodat u virtuele machines hebt gecontroleerd die gegevens naar beide verzenden. U moet de agent nog steeds toevoegen aan Azure Monitor voor VM's en de werkruimte configureren om aanvullende gegevens te verzamelen zoals hierboven is aangegeven, maar de virtuele machines kunnen hun bestaande beheerpakketten in een SCOM-omgeving blijven uitvoeren zonder wijzigingen.

Functies van Azure Monitor die een bestaande SCOM-functies uitbreiden, zijn onder andere:

- Gebruik Log Analytics om uw log- en prestatiegegevens interactief te analyseren.
- Gebruik logboekwaarschuwingen om waarschuwingsvoorwaarden voor meerdere virtuele machines te definiëren en langetermijntrends te gebruiken die niet mogelijk zijn met behulp van waarschuwingen in SCOM.   

Zie [Operations Manager verbinden met Azure Monitor](../platform/om-agents.md) voor meer informatie over het verbinden van uw bestaande SCOM-beheergroep met uw Log Analytics-werkruimte.


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het analyseren van gegevens in Azure Monitor-logboeken met behulp van logboekquery's.](../log-query/get-started-queries.md)
* [Meer informatie over waarschuwingen met behulp van statistieken en logboeken in Azure Monitor.](../platform/alerts-overview.md)

