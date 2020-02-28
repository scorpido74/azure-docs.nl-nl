---
title: Wat is er nieuw in Azure Monitor documentatie
description: Er zijn belang rijke updates voor Azure Monitor documentatie bijgewerkt voor elke maand.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/05/2020
ms.openlocfilehash: ecee13850e735f827a5465e0f49039f514afe233
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657738"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Wat is er nieuw in Azure Monitor documentatie?
In dit artikel vindt u een lijst met Azure Monitor-artikelen die nieuw zijn of aanzienlijk zijn bijgewerkt. Het wordt de eerste week van elke maand vernieuwd, zodat artikel updates van de vorige maand worden meegenomen.

## <a name="january-2020"></a>Januari 2020

### <a name="general"></a>Algemeen
- [Wat wordt er door Azure Monitor bewaakt?](monitor-reference.md) -Nieuw artikel.

### <a name="agents"></a>Agents
- [Verzamelen van logboek gegevens met Azure log Analytics-agent](platform/log-analytics-agent.md) -bijgewerkte tabel met netwerk firewall vereisten.


### <a name="alerts"></a>Waarschuwingen
- [Maak en beheer actie groepen in de Azure Portal](platform/action-groups.md) -instelling verwijderd voor v2-functies die niet meer nodig zijn.
- [Een waarschuwing voor metrische gegevens maken met een resource manager-sjabloon](platform/alerts-metric-create-templates.md) -voor beeld voor de para meter *ignoreDataBefore* .  Er zijn beperkingen toegevoegd voor regels met meerdere criteria.
- [Log Analytics waarschuwing gebruiken rest API](platform/api-alerts.md) -JSON-voor beeld is gecorrigeerd.


### <a name="application-insights"></a>Application Insights
- [IP-adressen die worden gebruikt door Application Insights en log Analytics](app/ip-addresses.md) : de sectie Beschik baarheid is bijgewerkt met informatie over het toevoegen van een binnenkomende poort regel voor het toestaan van verkeer via Azure-netwerk beveiligings groepen.
- [Problemen met Azure-toepassing Insights Profiler oplossen](app/profiler-troubleshooting.md) -bijgewerkte algemene probleem oplossing.
- De [bemonsterings-telemetrie in azure-toepassing Insights](app/sampling.md) is bijgewerkt en opnieuw gestructureerd om de Lees baarheid te verbeteren op basis van feedback van klanten.


### <a name="data-security"></a>Gegevensbeveiliging
- [Azure monitor door de klant beheerde sleutel configuratie](platform/customer-managed-keys.md) -nieuw artikel.

### <a name="insights-and-solutions"></a>Inzichten en oplossingen

#### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers
- [Azure monitor configureren voor het verzamelen van gegevens van containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config) , Details over het bijwerken van agents op Azure Red Hat open Shift en extra informatie toegevoegd om de methoden voor het bijwerken van de agent te onderscheiden.
- [Maak prestatie waarschuwingen voor Azure monitor voor containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-alerts) : herziene informatie en bijgewerkte stappen voor het maken van een waarschuwing voor prestatie gegevens die in de werk ruimte zijn opgeslagen met behulp van werk ruimte-context waarschuwingen.
- [Kubernetes bewaking met Azure monitor voor containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-analyze) : het artikel overzicht en het artikel analyseren met betrekking tot ondersteuning van Windows Kubernetes-clusters bijgewerkt.
- [Azure Red Hat open Shift-clusters configureren met Azure monitor voor containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-azure-redhat-setup) -Details toegevoegd voor het bijwerken van de agent op Azure Red Hat open Shift en extra informatie toevoegen om de methoden voor het bijwerken van de agent te onderscheiden.
- [Configureer hybride Kubernetes-clusters met Azure monitor voor containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-hybrid-setup) -bijgewerkt om toegevoegde ondersteuning voor beveiligde poort te weer spie gelen: 10250 met de CAdvisor van Kubelet.
- [Het beheren van de Azure monitor voor containers agent](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-manage-agent) -bijgewerkte details met betrekking tot gedrag en configuratie van metrische gegevens over metrieken met Azure Red Hat open Shift vergeleken met andere typen Kubernetes-clusters.
- [Azure monitor configureren voor containers Prometheus-integratie](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration) -bijgewerkte details met betrekking tot gedrag en configuratie van metrische uitval tijd met Azure Red Hat open Shift vergeleken met andere typen Kubernetes-clusters.
- [Het bijwerken van Azure monitor voor containers voor metrische](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-update-metrics) gegevens-bijgewerkte details met betrekking tot gedrag en configuratie van metrische uitval tijd met Azure Red Hat open Shift vergeleken met andere typen Kubernetes-clusters.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines
- [Veelgestelde vragen over Azure monitor voor VM's (ga)](insights/vminsights-ga-release-faq.md) : er is informatie toegevoegd over de upgrade van de werk ruimte en agents naar een nieuwe versie.

#### <a name="office-365"></a>Office 365
- [Office 365-beheer oplossing in azure](insights/solution-office-365.md) : extra informatie en veelgestelde vragen over het migreren naar Office 365-oplossing in azure Sentinel. Verwijderde onboarding sectie.



### <a name="logs"></a>Logboeken
- [Beheer log Analytics-werk ruimten in azure monitor](platform/manage-access.md) -updates op niet-acties.
- Het [gebruik en de kosten beheren voor Azure monitor-logboeken](platform/manage-cost-storage.md) -toegevoegde uitleg over de berekening van gegevens volume in de sectie prijs model.
- [Gebruik Azure Resource Manager sjablonen voor het maken en configureren van een log Analytics werk ruimte](platform/template-workspace-configuration.md) -bijgewerkte sjabloon met nieuwe prijs categorieën.


### <a name="platform-logs"></a>Platformlogboeken
- [Azure-activiteiten logboek verzamelen met Diagnostische instellingen-Azure monitor](platform/diagnostic-settings-legacy.md) -aanvullende informatie over gewijzigde eigenschappen.
- [Exporteer het Azure-activiteiten logboek](platform/activity-log-export.md) -bijgewerkt voor wijzigingen in de gebruikers interface. 





## <a name="december-2019"></a>December 2019

### <a name="agents"></a>Agents
- [Linux-computers verbinden met Azure monitor](platform/agent-linux.md) -nieuw artikel.

### <a name="alerts"></a>Waarschuwingen
- [Een waarschuwing voor metrische gegevens maken met een resource manager-sjabloon](platform/alerts-metric-create-templates.md) -voor beeld toegevoegd voor aangepaste metrische gegevens.
- [Het maken van waarschuwingen met dynamische drempel waarden in](platform/alerts-dynamic-thresholds.md) de sectie Azure monitor-added over het interpreteren van dynamische drempel grafieken.
- [Overzicht van waarschuwings-en meldings bewaking in azure](platform/alerts-overview.md) -bijgewerkte resource grafiek query.
- [Ondersteunde resources voor metrische waarschuwingen in azure monitor](platform/alerts-metric-near-real-time.md) -bijwerken naar metrische gegevens en dimensies die worden ondersteund.
- [Overschakelen van verouderde API voor log Analytics waarschuwingen naar de nieuwe Azure Alerts API](platform/alerts-log-api-switch.md) -toegevoegde notitie voor de gewijzigde naam van de waarschuwing.
- [Begrijpen hoe metrische waarschuwingen werken in Azure Monitor.](platform/alerts-metric-overview.md) -Ondersteunde resource typen zijn toegevoegd voor bewaking op schaal.

### <a name="application-insights"></a>Application Insights
- [Application Insights voor Worker-service-apps (niet-http-apps)](app/worker-service.md) -standaard C# logboek registratie niveau toegevoegd aan code. Bijgewerkte pakket referentie versie.
- [ApplicationInsights. config-referentie-Azure](app/configuration-with-applicationinsights-config.md) -bijgewerkte voorbeeld code.
- [Automatiseer Azure-toepassing inzichten met Power shell](app/powershell.md) -update naar Resource Manager-sjabloon.
- [Azure Monitor Application Insights NuGet-pakketten](app/nuget.md) -bijgewerkte pakket versies.
- [Een nieuwe Azure-toepassing Insights-resource maken](app/create-new-resource.md) : opmerking wordt toegevoegd aan een wereld wijd unieke naam.
- [Diagnose met Live Metrics stream-Azure-toepassing Insights](app/live-stream.md) -bijgewerkte ASP.net core SDK-versie vereiste.
- [Gebeurtenis tellers in Application Insights](app/eventcounters.md) -bijgewerkte categorie en tabel naar customMetrics.
- [Verken Java-traceer Logboeken in azure-toepassing inzichten](app/java-trace-logs.md) -toegevoegde configuratie voor de drempel waarde voor logboek registratie van Java Agent.
- [IP-adressen die worden gebruikt door Application Insights en log Analytics](app/ip-addresses.md) geactualiseerde IP-adressen voor Live Metrics stream.
- De [prestaties van Azure app Services](app/azure-web-apps.md) -ondersteuning voor ASP.net Core 3,0 controleren. 
- [Bewaak python-toepassingen met Azure monitor (preview)](app/opencensus-python.md) : toegevoegde verduidelijking voor de python-schema toewijzing van opentellingen aan Azure. Schema controleren
- [Release opmerkingen voor Azure-toepassing Insights](app/release-notes.md) -notities toegevoegd voor oudere versies.
- [Telemetrie-kanalen in azure-toepassing Insights](app/telemetry-channels.md) -bijgewerkte duur voor verwijderde gegevens gedurende een langere periode van de verbinding is verbroken.
- [Telemetrie-bemonstering in azure-toepassing Insights](app/sampling.md) -gecorrigeerd code fragment voor aangepaste TelemetryInitializer.
- [Problemen met Application Insights oplossen in een Java-webproject](app/java-troubleshoot.md) -verwijderde instructie over het niet ondersteunen van afhankelijkheids verzamelingen in JDK 9.

### <a name="insights-and-solutions"></a>Inzichten en oplossingen
- [Azure monitor voor containers Veelgestelde vragen](insights/container-insights-faq.md) : er is een vraag toegevoegd over de velden afbeelding en naam.
- [Azure SQL-analyse oplossing in azure monitor](insights/azure-sql.md) -bijgewerkte data base wacht op ondersteuning van beheerde exemplaren.
- [Azure monitor configureren voor het verzamelen van gegevens door containers agent](insights/container-insights-agent-config.md) -instellingen toegevoegd voor enrich_container_logs.
- [Configureer hybride Kubernetes-clusters met Azure monitor voor](insights/container-insights-hybrid-setup.md) de sectie voor het oplossen van problemen die door containers zijn toegevoegd.
- [Controleer Active Directory replicatie status met Azure monitor](insights/ad-replication-status.md) -.NET Framework vereiste update.
- [Netwerkprestatiemeter oplossing in door Azure](insights/network-performance-monitor.md) toegevoegde ondersteunde regio's.
- [Optimaliseer uw Active Directory-omgeving met Azure monitor](insights/ad-assessment.md) -.NET Framework vereiste updates.
- [Optimaliseer uw SQL Server-omgeving met Azure monitor](insights/sql-assessment.md) -.NET Framework vereiste updates.
- [Optimaliseer uw System Center Operations Manager-omgeving met Azure log Analytics](insights/scom-assessment.md) -.NET Framework vereiste updates.
- [Ondersteunde verbindingen met IT Service Management-connector in Azure log Analytics](platform/itsmc-connections.md) toegevoegde New York aan de vereiste client-id en client Secret.

### <a name="logs"></a>Logboeken
- [Azure log Analytics Workspace verwijderen en herstellen](platform/delete-workspace.md) -Power shell-methode is toegevoegd.
- Als [u uw Azure monitor ontwerpt, wordt het aantal implementaties](platform/design-logs-deployment.md) voor een werk ruimte verhoogd.

### <a name="metrics"></a>Metrische gegevens
- Azure Monitor de metrische gegevens van het [platform exporteerbaar via Diagnostische instellingen](platform/metrics-supported-export-diagnostic-settings.md) -nieuw artikel.

### <a name="platform-logs"></a>Platformlogboeken
Er zijn meerdere artikelen bijgewerkt als onderdeel van het herstructureren van inhoud voor platform logboeken op basis van een nieuwe functie voor het configureren van het activiteiten logboek met Diagnostische instellingen.

- [Azure-resource logboeken archiveren in een opslag account](platform/resource-logs-collect-storage.md)
- [Gebeurtenis schema voor Azure-activiteiten logboek](platform/activity-log-schema.md)
- [Azure Monitor-service limieten](service-limits.md)
- [Azure-activiteiten Logboeken in Log Analytics werk ruimte verzamelen en analyseren](platform/activity-log-collect.md)
- [Azure-activiteiten logboek verzamelen met Diagnostische instellingen (preview)-Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Azure-activiteiten logboeken verzamelen in een Log Analytics werkruimte over Azure-tenants](platform/activity-log-collect-tenants.md)
- [Azure-resource logboeken verzamelen in Log Analytics werk ruimte](platform/resource-logs-collect-workspace.md)
- [Een diagnostische instelling maken in azure met behulp van de Resource Manager-sjabloon](platform/diagnostic-settings-template.md)
- [Diagnostische instelling maken voor het verzamelen van Logboeken en metrische gegevens in azure](platform/diagnostic-settings.md)
- [Het Azure-activiteiten logboek exporteren](platform/activity-log-export.md)
- [Overzicht van Azure platform-logboeken](platform/platform-logs-overview.md)
- [Azure-bewakings gegevens streamen naar Event Hub](platform/stream-monitoring-data-event-hubs.md)
- [Azure-platform logboeken streamen naar een Event Hub](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Snelstarts en zelfstudies

- [Maak een grafiek met metrische gegevens in azure monitor](learn/tutorial-metrics-explorer.md) -nieuw artikel.
- [Verzamel bron logboeken van een Azure-resource en analyseer met Azure monitor](learn/tutorial-resource-logs.md) -nieuw artikel.
- [Een Azure-resource bewaken met Azure monitor](learn/quick-monitor-azure-resource.md) -nieuw artikel.
   
## <a name="next-steps"></a>Volgende stappen

- Als u een bijdrage wilt leveren aan Azure Monitor documentatie, raadpleegt u de [hand leiding voor docs-inzenders](https://docs.microsoft.com/contribute/).