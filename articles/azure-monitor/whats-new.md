---
title: Nieuwe nieuwe artikelen in Azure Monitor-documentatie
description: Elke maand worden belangrijke updates voor azure monitor-documentatie bijgewerkt.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: a2e6d2a459a6713aa9372496fc3a933c0a886ed9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802601"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Wat is er nieuw in Azure Monitor-documentatie?
In dit artikel vindt u lijsten met Azure Monitor-artikelen die nieuw zijn of aanzienlijk zijn bijgewerkt. Het zal worden vernieuwd de eerste week van elke maand om artikel updates van de vorige maand op te nemen.

## <a name="march-2020"></a>Maart 2020

### <a name="general"></a>Algemeen
- [Overzicht van Azure Monitor](overview.md) - Overzichtsvideo voor Azure Monitor toegevoegd.
- [Azure Monitor door de klant beheerde sleutelconfiguratie](platform/customer-managed-keys.md) - Algemene updates.
- [Azure Monitor gegevensreferentie](/azure/azure-monitor/reference) - Nieuwe site.

### <a name="alerts"></a>Waarschuwingen

- [Waarschuwingen voor activiteitenlogboeken maken, weergeven en beheren in Azure Monitor](platform/alerts-activity-log.md) - Aanvullende uitleg van de sjabloon Resourcebeheer.
- [Begrijpen hoe metrische waarschuwingen werken in Azure Monitor.](platform/alerts-metric-overview.md) - Bijgewerkt voor overheidssteun.
- [Problemen met Azure Monitor-waarschuwingen en -meldingen oplossen](platform/alerts-troubleshoot.md) - Nieuw artikel

### <a name="application-insights"></a>Application Insights
- [Automatiseer Azure Application Insights met PowerShell](app/powershell.md) - Added ARMClient examples.
- [Continue export van telemetrie vanuit Application Insights](app/export-telemetry.md) - Tabel toevoegen met details van de exportstructuur.
- [Snapshot Debugger inschakelen voor .NET-apps in Azure App Service](app/snapshot-debugger-appservice.md) - Voorbeeld van de sjabloon Added Resource Manager.
- [Beheer het gebruik en de kosten voor Azure Application Insights](app/pricing.md) - Extra informatie over de waarschuwing voor gegevensdop.
- [Python-toepassingen controleren met Azure Monitor (preview)](app/opencensus-python.md) - Standaardstatistieken toegevoegd.
- [Bronkaartondersteuning voor JavaScript-toepassingen - Azure Monitor Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/source-map-support) - Nieuw artikel.


### <a name="containers"></a>Containers
- [Veelgestelde vragen over Azure Monitor](faq.md) - Bijwerken voor Azure Monitor voor containers.
- [GPU-bewaking configureren met Azure Monitor voor containers](insights/container-insights-gpu-monitoring.md) - Nieuw artikel

### <a name="insights"></a>Inzichten
- [Office 365-beheeroplossing in Azure](insights/solution-office-365.md) - Bijgewerkte afschrijvingsdatum.

### <a name="logs"></a>Logboeken
- [Logquery's optimaliseren in Azure Monitor](log-query/query-optimization.md) - Added CPU condition for XML and JSON parsing.
- [Azure Log Analytics-werkruimte verwijderen en herstellen](platform/delete-workspace.md) - Probleemoplossing toegevoegd.
- [Gebruik Azure Monitor Logs met Azure Logic Apps en Power Automate](platform/logicapp-flow-connector.md) - Bijgewerkt voor nieuwe Azure Monitor connector.

### <a name="metrics"></a>Metrische gegevens
- [Afschaffing van schijfstatistieken in de Azure-portal](https://docs.microsoft.com/azure/azure-monitor/platform/portal-disk-metrics-deprecation) - Nieuw artikel.
- [Zelfstudie - Maak een metrische grafiek in Azure Monitor](learn/tutorial-metrics-explorer.md) - Toegevoegde video.

### <a name="platform-logs"></a>Platformlogboeken
- [Azure-activiteitenlogboek verzamelen en analyseren in Azure Monitor](platform/activity-log-collect.md) - Opnieuw schrijven om het verzamelen van activiteitslogboeken beter uit te leggen met diagnostische instellingen.

### <a name="virtual-machines"></a>Virtuele machines
- [Azure virtual machines monitoren met Azure Monitor](insights/monitor-vm-azure.md) - Nieuw artikel.
- [Snelstart: Azure-virtuele machines controleren met Azure Monitor](learn/quick-monitor-azure-vm.md) - Bijgewerkt om Azure-monitor voor VM's toe te voegen.
- [Waarschuwingen van Azure Monitor voor VM's](insights/vminsights-alerts.md) - Nieuw artikel
- [Overzicht van Azure Monitor voor VM's inschakelen](insights/vminsights-enable-overview.md) - Bijgewerkte koppelingskoppelingen voor agents.


Algemene updates voor algemene beschikbaarheid van Azure Monitor voor VM's
- [Wat is Azure Monitor voor VM's?](insights/vminsights-overview.md)
- [Veelgestelde vragen over Azure Monitor for VM's (GA)](insights/vminsights-ga-release-faq.md) 
- [Azure-monitor voor VM's inschakelen met Azure-beleid](insights/vminsights-enable-at-scale-policy.md) 
- [Prestaties in kaart brengen met Azure Monitor voor VM's](insights/vminsights-performance.md)
- [Logboeken opvragen vanuit Azure-monitor voor VM's](insights/vminsights-log-search.md)
- [App-afhankelijkheden weergeven met Azure Monitor voor VM's](insights/vminsights-maps.md) 


### <a name="visualizations"></a>Visualisaties
- [Gegevens van Azure Monitor visualiseren](visualizations.md) - Bijgewerkt om geplande afschrijving van View Designer op te merken.





## <a name="february-2020"></a>Februari 2020

### <a name="agents"></a>Agents
Meerdere updates als onderdeel van het herschrijven van inhoud van diagnostische extensie.

- [Overzicht van de Azure-bewakingsagents](platform/agents-overview.md) - Geherstructureerde tabellen om de unieke functies van elke agent beter te verduidelijken.
- [Overzicht van azure Diagnostics-extensie](platform/diagnostics-extension-overview.md) - Volledig herschrijven.
- [Gebruik blob-opslag voor IIS en tabelopslag voor gebeurtenissen in Azure Monitor](platform/diagnostics-extension-logs.md) - Algemeen herschrijven voor update en duidelijkheid.
- [Windows Azure diagnostic extension (WAD) installeren en configureren -](platform/diagnostics-extension-windows-install.md) Nieuw artikel. 
- [Windows-schema voor diagnostische extensie](platform/diagnostics-extension-schema-windows.md) - Gereorganiseerd.
- [Gegevens van windows Azure-diagnostische extensie verzenden naar Azure Event Hubs](platform/diagnostics-extension-stream-event-hubs.md) - Volledig herschreven en bijgewerkt.
- [Diagnostische gegevens opslaan en weergeven in Azure Storage](/azure/cloud-services/diagnostics-extension-to-storage) - Volledig herschreven en bijgewerkt.
- [Log Analytics virtual machine extension for Windows](../virtual-machines/extensions/oms-windows.md) - Better verduidelijkt de relatie met Log Analytics agent.
- [Azure Monitor virtual machine extension for Linux](../virtual-machines/extensions/oms-linux.md) - Better verduidelijkt de relatie met Log Analytics agent.




### <a name="application-insights"></a>Application Insights
- [Verbindingstekenreeksen in Azure Application Insights](app/sdk-connection-string.md) - Nieuw artikel.

### <a name="insights-and-solutions"></a>Inzichten en oplossingen

#### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers
- [Integreer Azure Active Directory met Azure Kubernetes Service](../aks/azure-ad-integration.md) - Added note for creating a client application to support RBAC-enabled cluster to support Azure Monitor for containers.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines
- [Veelgestelde vragen van Azure Monitor for VM's (GA)](insights/vminsights-ga-release-faq.md) wijzigen : wijzig de manier waarop prestatiegegevens worden opgeslagen.

#### <a name="office-365"></a>Office 365
- [Office 365-beheeroplossing in Azure](insights/solution-office-365.md) - Bijgewerkte afschrijvingsdatum.


### <a name="logs"></a>Logboeken
- [Logquery's optimaliseren in Azure Monitor](log-query/query-optimization.md) - Nieuw artikel.
- [Beheer het gebruik en de kosten voor Azure Monitor-logboeken](platform/manage-cost-storage.md) - Verbeterde voorbeeldquery's om inzicht te krijgen in uw gebruik.

### <a name="metrics"></a>Metrische gegevens
- [Azure Monitor-platformstatistieken die kunnen worden geëxporteerd via diagnostische instellingen](platform/metrics-supported-export-diagnostic-settings.md) - Sectie toegevoegd over wijzigingen in gedrag voor nullen en nulwaarden.


### <a name="visualizations"></a>Visualisaties
Meerdere nieuwe artikelen voor weergave-conversiegids voor werkmappen.

- [Azure Monitor-weergave overgangshandleiding voor werkmappen naar werkmappen](platform/view-designer-conversion-overview.md) - Nieuw artikel.
- [Azure Monitor-weergave conversieopties voor services voor werkmappen](platform/view-designer-conversion-options.md) - Nieuw artikel.
- [Azure Monitor-weergave ontwerper naar werkmappentegelconversies](platform/view-designer-conversion-tiles.md) - Nieuw artikel.
- [Azure Monitor-weergave ontwerper naar werkmappen conversieoverzicht en toegang -](platform/view-designer-conversion-access.md) Nieuw artikel.
- [Azure Monitor-weergave ontwerper naar werkmappen conversie algemene taken](platform/view-designer-conversion-tasks.md) - Nieuw artikel.
- [Voorbeelden van azure monitor-weergave-conversievoor werkmappen](platform/view-designer-conversion-examples.md) - Nieuw artikel.




## <a name="january-2020"></a>Januari 2020

### <a name="general"></a>Algemeen
- [Wat wordt gecontroleerd door Azure Monitor?](monitor-reference.md) - Nieuw artikel.

### <a name="agents"></a>Agents
- [Loggegevens verzamelen met Azure Log Analytics-agent](platform/log-analytics-agent.md) - Tabel Bijgewerkte netwerkfirewallvereisten.


### <a name="alerts"></a>Waarschuwingen
- [Actiegroepen maken en beheren in de Azure-portal](platform/action-groups.md) - Instelling verwijderd voor v2-functies die niet langer nodig zijn.
- [Maak een metrische waarschuwing met een resourcemanagersjabloon](platform/alerts-metric-create-templates.md) - Voorbeeld toegevoegd voor de parameter *ignoreDataBefore.*  Extra beperkingen voor regels voor meerdere criteria.
- [Met behulp van Log Analytics Alert REST API](platform/api-alerts.md) - JSON voorbeeld gecorrigeerd.


### <a name="application-insights"></a>Application Insights
- [IP-adressen die worden gebruikt door Application Insights en Log Analytics](app/ip-addresses.md) - De sectie Beschikbaarheidstest bijgewerkt met hoe u een inkomende poortregel toevoegt om verkeer toe te staan met Azure Network Security Groups.
- [Problemen met Azure Application Insights Profiler oplossen](app/profiler-troubleshooting.md) - Algemene probleemoplossing bijgewerkt.
- [Telemetriebemonstering in Azure Application Insights](app/sampling.md) - Bijgewerkt en geherstructureerd om de leesbaarheid te verbeteren op basis van feedback van klanten.


### <a name="data-security"></a>Gegevensbeveiliging
- [Azure Monitor door de klant beheerde sleutelconfiguratie](platform/customer-managed-keys.md) - Nieuw artikel.

### <a name="insights-and-solutions"></a>Inzichten en oplossingen

#### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers
- [Azure Monitor configureren voor het verzamelen van containersagent](insights/container-insights-agent-config.md) - Details toegevoegd voor upgradeagent op Azure Red Hat OpenShift en aanvullende informatie toegevoegd om de methoden voor het upgraden van agent te onderscheiden.
- [Prestatiewaarschuwingen maken voor Azure Monitor voor containers](insights/container-insights-alerts.md) - Herziene informatie en bijgewerkte stappen voor het maken van een waarschuwing voor prestatiegegevens die zijn opgeslagen in de werkruimte met behulp van waarschuwingen voor werkruimtecontext.
- [Kubernetes-monitoring met Azure Monitor voor containers](insights/container-insights-analyze.md) - Bijgewerkt zowel het overzichtsartikel als het analyseartikel over ondersteuning van Windows Kubernetes-clusters.
- [Azure Red Hat OpenShift-clusters configureren met Azure Monitor voor containers](insights/container-insights-azure-redhat-setup.md) - Extra details voor upgradeagent op Azure Red Hat OpenShift en aanvullende informatie toegevoegd om de methoden voor het upgraden van agent te onderscheiden.
- [Hybride Kubernetes-clusters configureren met Azure Monitor voor containers](insights/container-insights-hybrid-setup.md) - Bijgewerkt om extra ondersteuning voor beveiligde poort:10250 weer te geven met de cAdvisor van de Kubelet.
- [De Azure Monitor voor containersagent beheren](insights/container-insights-manage-agent.md) - Bijgewerkte details met betrekking tot gedrag en config van metrische schrapen met Azure Red Hat OpenShift in vergelijking met andere typen Kubernetes-clusters.
- [Azure Monitor configureren voor containers Prometheus-integratie](insights/container-insights-prometheus-integration.md) - Bijgewerkte details met betrekking tot gedrag en config van metrische schrapen met Azure Red Hat OpenShift in vergelijking met andere typen Kubernetes-clusters.
- [Azure Monitor bijwerken voor containers voor statistieken](insights/container-insights-update-metrics.md) - Bijgewerkte details met betrekking tot gedrag en config van metrische schrapen met Azure Red Hat OpenShift in vergelijking met andere typen Kubernetes-clusters.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines
- [Veelgestelde vragen van Azure Monitor for VM's (GA)](insights/vminsights-ga-release-faq.md) - Informatie toegevoegd over het upgraden van werkruimte en agents naar een nieuwe versie.

#### <a name="office-365"></a>Office 365
- [Office 365-beheeroplossing in Azure](insights/solution-office-365.md) - Details en veelgestelde vragen over migratie naar Office 365-oplossing in Azure Sentinel. Verwijderde onboarding sectie.



### <a name="logs"></a>Logboeken
- [Log Analytics-werkruimten beheren in Azure Monitor](platform/manage-access.md) - Updates voor niet-acties.
- [Gebruik en kosten voor Azure Monitor Logs beheren](platform/manage-cost-storage.md) - Extra verduidelijking over de berekening van het gegevensvolume in de sectie Prijsmodel.
- [Azure Resource Manager-sjablonen gebruiken om een logboekanalysewerkruimte te maken en te configureren](platform/template-workspace-configuration.md) - Bijgewerkte sjabloon met nieuwe prijsniveaus.


### <a name="platform-logs"></a>Platformlogboeken
- [Azure Activity-logboek verzamelen met diagnostische instellingen- Azure Monitor](platform/diagnostic-settings-legacy.md) - Aanvullende informatie over gewijzigde eigenschappen.
- [Het Azure-activiteitenlogboek exporteren](platform/activity-log-export.md) - Bijgewerkt voor wijzigingen in de gebruikersinterface. 





## <a name="december-2019"></a>December 2019

### <a name="agents"></a>Agents
- [Linux-computers verbinden met Azure Monitor](platform/agent-linux.md) - Nieuw artikel.

### <a name="alerts"></a>Waarschuwingen
- [Maak een metrische waarschuwing met een resourcemanagersjabloon](platform/alerts-metric-create-templates.md) - Voorbeeld toegevoegd voor aangepaste statistiek.
- [Waarschuwingen maken met dynamische drempelwaarden in Azure Monitor](platform/alerts-dynamic-thresholds.md) - Sectie toegevoegd voor het interpreteren van dynamische drempeldiagrammen.
- [Overzicht van waarschuwingen en meldingsbewaking in Azure](platform/alerts-overview.md) - Bijgewerkte resourcegrafiekquery.
- [Ondersteunde resources voor metrische waarschuwingen in Azure Monitor](platform/alerts-metric-near-real-time.md) - Bijwerken naar ondersteunde statistieken en dimensies.
- [Schakel over van de API voor verouderde Logboekanalysewaarschuwingen naar de nieuwe Azure Alerts API](platform/alerts-log-api-switch.md) - Notitie toegevoegd op gewijzigde waarschuwingsnaam.
- [Begrijpen hoe metrische waarschuwingen werken in Azure Monitor.](platform/alerts-metric-overview.md) - Ondersteunde resourcetypen toegevoegd voor monitoring op schaal.

### <a name="application-insights"></a>Application Insights
- [Application Insights for Worker Service-apps (niet-HTTP-apps)](app/worker-service.md) - Standaardlogboekregistratieniveau toegevoegd aan C#-code. Bijgewerkte pakketreferentieversie.
- [ApplicationInsights.config reference - Azure](app/configuration-with-applicationinsights-config.md) - Updated sample code.
- [Automatiseer Azure Application Insights met PowerShell](app/powershell.md) - Update naar Resource Manager-sjabloon.
- [Azure Monitor Application Insights NuGet-pakketten](app/nuget.md) - Bijgewerkte pakketversies.
- [Maak een nieuwe Azure Application Insights-bron](app/create-new-resource.md) - Opmerking toegevoegd aan een unieke naam wereldwijd.
- [Diagnose met Live Metrics Stream - Azure Application Insights](app/live-stream.md) - Bijgewerkt ASP.NET Core SDK-versie vereiste.
- [Gebeurtenistellers in Application Insights](app/eventcounters.md) - Bijgewerkte categorie en tabel naar customMetrics.
- [Java trace logs verkennen in Azure Application Insights](app/java-trace-logs.md) - Added configuration for Java agent logging threshold.
- [IP-adressen die worden gebruikt door Application Insights en Log Analytics](app/ip-addresses.md) - Bijgewerkte IP-adressen voor Live Metrics Stream.
- [Houd de prestaties van Azure-app-services in de gaten](app/azure-web-apps.md) - Ondersteuning toegevoegd voor ASP.NET Core 3.0. 
- [Python-toepassingen bewaken met Azure Monitor (preview)](app/opencensus-python.md) - Added clarification for OpenCensus Python-schematoewijzing voor Azure . Monitorschema
- [Release notes for Azure Application Insights](app/release-notes.md) - Added notes for older releases.
- [Telemetriekanalen in Azure Application Insights](app/telemetry-channels.md) - Bijgewerkte duur voor verwijderde gegevens gedurende een langere periode van verloren verbinding.
- [Telemetriebemonstering in Azure Application Insights](app/sampling.md) - Gecorrigeerd codefragment voor aangepaste telemetrieInitializer.
- [Problemen met toepassingsinzichten oplossen in een Java-webproject](app/java-troubleshoot.md) - Verwijderde instructie over het niet ondersteunen van afhankelijkheidsverzameling in JDK 9.

### <a name="insights-and-solutions"></a>Inzichten en oplossingen
- [Azure Monitor voor containers Veelgestelde vragen](insights/container-insights-faq.md) - Toegevoegde vraag op de velden Afbeelding en naam.
- [Azure SQL Analytics-oplossing in Azure Monitor](insights/azure-sql.md) - Bijgewerkte database wacht ondersteuning voor beheerde instantie.
- [Azure Monitor configureren voor het verzamelen van containersagentgegevens](insights/container-insights-agent-config.md) - Instelling toegevoegd voor enrich_container_logs.
- [Hybride Kubernetes-clusters configureren met Azure Monitor voor containers](insights/container-insights-hybrid-setup.md) - Sectie Probleemoplossing toegevoegd.
- [Controleer de status van Active Directory-replicatie met Azure Monitor](insights/ad-replication-status.md) - vereiste .NET Framework bijgewerkt.
- [Oplossing voor netwerkprestatiemonitor in Azure](insights/network-performance-monitor.md) - Ondersteunde regio's toegevoegd.
- [Optimaliseer uw Active Directory-omgeving met Azure Monitor](insights/ad-assessment.md) - Vereiste .NET Framework bijgewerkt.
- [Optimaliseer uw SQL Server-omgeving met Azure Monitor](insights/sql-assessment.md) - Vereiste .NET Framework bijgewerkt.
- [Optimaliseer uw System Center Operations Manager-omgeving met Azure Log Analytics](insights/scom-assessment.md) - vereiste .NET Framework bijgewerkt.
- [Ondersteunde verbindingen met IT Service Management Connector in Azure Log Analytics](platform/itsmc-connections.md) - New York toegevoegd aan vereiste client-ID en clientgeheim.

### <a name="logs"></a>Logboeken
- [Azure Log Analytics-werkruimte verwijderen en herstellen](platform/delete-workspace.md) - PowerShell-methode toegevoegd.
- [Het ontwerpen van de implementatie van Azure Monitor Logs](platform/design-logs-deployment.md) - Opnamesnelheid voor een werkruimte is verhoogd.

### <a name="metrics"></a>Metrische gegevens
- [Azure Monitor-platformstatistieken die kunnen worden geëxporteerd via diagnostische instellingen](platform/metrics-supported-export-diagnostic-settings.md) - Nieuw artikel.

### <a name="platform-logs"></a>Platformlogboeken
Meerdere artikelen bijgewerkt als onderdeel van de herstructurering van de inhoud voor platformlogboeken op basis van nieuwe functie voor het configureren van activiteitslogboek met behulp van diagnostische instellingen.

- [Azure-bronlogboeken archiveren naar opslagaccount](platform/resource-logs-collect-storage.md)
- [Gebeurtenisschema azure-activiteitslogboek](platform/activity-log-schema.md)
- [Azure Monitor-servicelimieten](service-limits.md)
- [Azure-activiteitslogboeken verzamelen en analyseren in de werkruimte Log Analytics](platform/activity-log-collect.md)
- [Azure-activiteitslogboek verzamelen met diagnostische instellingen (voorbeeld) - Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Azure Activity-logboeken verzamelen in een Logboekanalysewerkruimte voor Azure-tenants](platform/activity-log-collect-tenants.md)
- [Azure-bronlogboeken verzamelen in de werkruimte Log Analytics](platform/resource-logs-collect-workspace.md)
- [Diagnostische instelling maken in Azure met behulp van resourcebeheersjabloon](platform/diagnostic-settings-template.md)
- [Diagnostische instelling maken om logboeken en statistieken in Azure te verzamelen](platform/diagnostic-settings.md)
- [Het Azure-activiteitenlogboek exporteren](platform/activity-log-export.md)
- [Overzicht van Azure-platformlogboeken](platform/platform-logs-overview.md)
- [Azure-bewakingsgegevens streamen naar gebeurtenishub](platform/stream-monitoring-data-event-hubs.md)
- [Azure-platformlogboeken streamen naar een gebeurtenishub](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Snelstarts en zelfstudies

- [Maak een metrische grafiek in Azure Monitor](learn/tutorial-metrics-explorer.md) - Nieuw artikel.
- [Verzamel bronlogboeken van een Azure Resource en analyseer met Azure Monitor](learn/tutorial-resource-logs.md) - Nieuw artikel.
- [Monitor een Azure-bron met Azure Monitor](learn/quick-monitor-azure-resource.md) - Nieuw artikel.
   
## <a name="next-steps"></a>Volgende stappen

- Zie de [handleiding voor documenteninleveren](https://docs.microsoft.com/contribute/)als u wilt bijdragen aan azure monitor-documentatie.