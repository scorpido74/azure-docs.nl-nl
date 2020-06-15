---
title: Wat is er nieuw de documentatie van Azure Monitor?
description: Belangrijke updates voor Azure Monitor, documentatie wordt elke maand bijgewerkt.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 04/06/2020
ms.openlocfilehash: 561767b6857f4b52b3ee1eb17fedd51ad409ee29
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84462971"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Wat is er nieuw de documentatie van Azure Monitor?

In dit artikel vindt u een lijst met Azure Monitor-artikelen die nieuw of aanzienlijk bijgewerkt zijn. Elke eerste week van de maand wordt de lijst bijgewerkt met artikelupdates van de vorige maand.

## <a name="may-2020"></a>Mei 2020

### <a name="general"></a>Algemeen

- [Veelgestelde vragen over Azure Monitor](faq.md): toegevoegde sectie voor metrische gegevens.
- [Door de klant beheerde Azure Monitor-sleutel](platform/customer-managed-keys.md): verschillende wijzigingen in voorbereiding van algemene beschikbaarheid.
- [Ingebouwde beleidsdefinities voor Azure Monitor](samples/policy-samples.md): nieuw artikel.
- [Opslagaccounts van klanten voor logboekopname](platform/private-storage.md): nieuw artikel.
- [Gebruik en kosten beheren voor Azure Monitor-logboeken](platform/manage-cost-storage.md): proportionele clusterfacturering toegevoegd.
- [Azure Private Link gebruiken om netwerken veilig te verbinden met Azure Monitor](platform/private-link-security.md): nieuw artikel.


#### <a name="new-resource-manager-template-samples"></a>Nieuwe Azure Resource Manager-voorbeeldsjablonen 
- [Resource Manager-voorbeeldsjablonen voor Azure Monitor](samples/resource-manager-samples.md)
- [Resource Manager-voorbeeldsjablonen voor actiegroepen](samples/resource-manager-action-groups.md)
- [Resource Manager-voorbeeldsjablonen voor agents](samples/resource-manager-agent.md)
- [Resource Manager-voorbeeldsjablonen voor Azure Monitor voor containers](samples/resource-manager-container-insights.md)
- [Resource Manager-voorbeeldsjablonen voor Azure Monitor voor VM’s](samples/resource-manager-vminsights.md)
- [Resource Manager-voorbeeldsjablonen voor diagnostische instellingen](samples/resource-manager-diagnostic-settings.md)
- [Resource Manager-voorbeeldsjablonen voor Log Analytics-werkruimten](samples/resource-manager-workspace.md)
- [Resource Manager-voorbeeldsjablonen voor logboekquery’s](samples/resource-manager-log-queries.md)
- [Resource Manager-voorbeeldsjablonen voor logboekquerywaarschuwingen](samples/resource-manager-alerts-log.md)
- [Resource Manager-voorbeeldsjablonen voor metrische waarschuwingen](samples/resource-manager-alerts-metric.md)
- [Resource Manager-voorbeeldsjablonen voor werkmappen](samples/resource-manager-workbooks.md)

### <a name="agents"></a>Agents
- [WAD-extensie (Windows Azure Diagnostics) installeren en configureren](platform/diagnostics-extension-windows-install.md): meer details toegevoegd voor het configureren van diagnostische gegevens.
- [Overzicht Logboekanalyse-agent](platform/log-analytics-agent.md): ondersteunde Linux-versies toegevoegd.

### <a name="application-insights"></a>Application Insights

- [Toepassingen bewaken die worden uitgevoerd op Azure Functions met Application Insights, Azure Monitor](app/monitor-functions.md): nieuw artikel.
- [Node.js-services bewaken met Azure Application Insights](app/nodejs.md): algemene updates met inbegrip van nieuwe sectie over migratie van eerdere versies.
- [IP-adressen gebruikt door Application Insights en logboekanalyse](app/ip-addresses.md): IP-adressen toegevoegd voor webhooks en de Amerikaanse overheid.
- [Toepassingen bewaken op AKS (Azure Kubernetes Service) met Application Insights, Azure Monitor](app/kubernetes-codeless.md): nieuw artikel.
- [Problemen met ontbrekende gegevens oplossen, Application Insights voor .NET](app/asp-net-troubleshoot-no-data.md): toegevoegde sectie over het verzamelen van logboeken met dotnet-trace.
- [Wijzigingsanalyse voor toepassingen gebruiken in Azure Monitor om problemen met web-apps te vinden](app/change-analysis.md): meerdere updates in de functie Wijzigingsanalyse.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Nieuwe en bijgewerkte artikelen voor preview van op werkruimte gebaseerde toepassingen
- [Op werkruimte gebaseerd resourceschema van Azure Monitor Application Insights](app/apm-tables.md)
- [Een nieuwe op werkruimte gebaseerde resource voor Azure Monitor Application Insights maken](app/create-workspace-resource.md)
- [Expressie app() in Azure Monitor-logboekquery's](log-query/app-expression.md)
- [Query-bereik vastleggen in Azure Monitor-logboekanalyse](log-query/scope.md)
- [Query's uitvoeren op resources met Azure Monitor](log-query/cross-workspace-query.md)
- [Standaardeigenschappen in Azure Monitor-logboekrecords](platform/log-standard-properties.md)
- [Structuur van Azure Monitor-logboeken](log-query/logs-structure.md)





### <a name="containers"></a>Containers
- [Azure Monitor voor containers inschakelen](insights/container-insights-onboard.md): bijgewerkte firewall-configuratietabel.
- [Azure Monitor voor containers bijwerken voor metrische gegevens](insights/container-insights-update-metrics.md): update voor gebruik van beheerde identiteiten om metrische gegevens te verzamelen.
- [Kosten bewaken voor Azure Monitor voor containers](insights/container-insights-cost.md): nieuw artikel.
- [Azure Monitor voor containers Live Data (preview) instellen](insights/container-insights-livedata-setup.md): ondersteuning voor nieuwe clusterrolbinding.

### <a name="insights"></a>Inzichten
- [Azure Monitor voor Azure Cache voor Redis (preview)](insights/redis-cache-insights-overview.md): nieuw artikel.
- [Sleutelkluis bewaken met Azure Monitor voor Key Vault (preview)](insights/key-vaults-insights-overview.md): nieuw artikel.

### <a name="logs"></a>Logboeken
- [Logboekanalyse maken en configureren met PowerShell](platform/powershell-workspace-configuration.md): sectie probleemoplossing toegevoegd.
- [Een Log Analytics-werkruimte maken in de Azure-portal](learn/quick-create-workspace.md): sectie probleemoplossing toegevoegd.
- [Een Log Analytics-werkruimte maken met Azure CLI](learn/quick-create-workspace-cli.md): sectie probleemoplossing toegevoegd.
- [Azure Log Analytics-werkruimte verwijderen en herstellen](platform/delete-workspace.md): bijgewerkte informatie over het herstellen van een verwijderde werkruimte.
- [Functies in Azure Monitor-logboekquery's](log-query/functions.md): notitie verwijderd over functies die geen andere functies bevatten.
- [Structuur va Azure Monitor-logboeken](log-query/logs-structure.md): eigenschapsbeschrijvingen verduidelijkt voor Application Insights-tabel.
- [Azure Monitor-logboeken gebruiken met Azure Logic Apps en Power Automate](platform/logicapp-flow-connector.md): sectie met limieten toegevoegd.
- [PowerShell gebruiken om een Log Analytics-werkruimte te maken en te configureren](platform/powershell-workspace-configuration.md): sectie probleemoplossing toegevoegd.


### <a name="metrics"></a>Metrische gegevens
- [Door Azure Monitor ondersteunde metrische gegevens per resourcetype](platform/metrics-supported.md): metrische gegevens van gasten en routering van metrische gegevens verduidelijkt. 

### <a name="solutions"></a>Oplossingen
- [Uw Active Directory-omgeving optimaliseren met Azure Monitor](insights/ad-assessment.md): Windows Server 2019 toegevoegd aan ondersteunde versies.
- [Uw SQL Server-omgeving optimaliseren met Azure Monitor](insights/sql-assessment.md): ondersteunde versies van SQL Server toegevoegd.


### <a name="virtual-machines"></a>Virtuele machines
- [Overzicht van Azure Monitor voor VM's inschakelen](insights/vminsights-enable-overview.md): ondersteunde versies van Ubuntu Server toegevoegd. Ondersteunde regio's voor Log Analytics-werkruimte toegevoegd.
- [Prestaties in kaart brengen met Azure Monitor voor VM's](insights/vminsights-performance.md): sectie beperkingen toegevoegd voor niet-beschikbare metrische gegevens.

### <a name="visualizations"></a>Visualisaties
- [Azure Monitor-werkmappen en Azure Resource Manager-sjablonen](platform/workbooks-automate.md): Resource Manager-update toegevoegd voor implementeren van een workbooksjabloon.
- [Azure Monitor-workbookgroepen](platform/workbooks-groups.md): nieuw artikel.
- [Azure Monitor-workbooks, JSON-gegevens transformeren met JSONPath](platform/workbooks-jsonpath.md): nieuw artikel.


## <a name="april-2020"></a>April 2020

### <a name="general"></a>Algemeen

- [Door de klant beheerde sleutel in Azure Monitor](platform/customer-managed-keys.md): sectie over asynchrone bewerkingen toegevoegd.
- [Log Analytics-werkruimten beheren in Azure Monitor](platform/manage-access.md): aangepaste logboeksecties bijgewerkt.

### <a name="alerts"></a>Waarschuwingen

- [Actieregels voor Azure Monitor-waarschuwingen](platform/alerts-action-rules.md): video toegevoegd.
- [Overzicht van bewaken van waarschuwingen en meldingen in Azure](platform/alerts-overview.md): video toegevoegd.

### <a name="application-insights"></a>Application Insights

- [Toepassingsoverzicht in Azure Application Insights](app/app-map.md): configuratie van cloudrolnamen toegevoegd voor java-agent.
- [Azure Application Insights .NET Agent API-verwijzing](app/status-monitor-v2-api-reference.md): API-verwijzingen gecombineerd.
- [IP-adressen die worden gebruikt door Application Insights en Logboekanalyse](app/ip-addresses.md): IP-adressen bijgewerkt voor AppInsights- en Logboekanalyse-API's, actiegroepwebhooks, Azure Amerikaanse overheid.
- [Java-toepassingen overal bewaken](app/java-standalone-config.md): nieuw artikel.
- [Java-toepassingen op elke omgeving bewaken](app/java-in-process-agent.md): nieuw artikel.
- [Java-toepassingen die worden uitgevoerd in elke omgeving bewaken](app/java-standalone-arguments.md): nieuw artikel.
- [Java-toepassingen bewaken die on-premises worden uitgevoerd](app/java-on-premises.md): nieuw artikel.
- [Application Insights in Visual Studio verwijderen](app/remove-application-insights.md): nieuw artikel.
- [Steekproef nemen van telemetrie in Azure Application Insights](app/sampling.md): correctie in steekproef met vaste frequentie in Python.

### <a name="containers"></a>Containers

- [Azure Red Hat OpenShift v4.x configureren met Azure Monitor voor containers](insights/container-insights-azure-redhat4-setup.md): nieuw artikel.
- [Handmatig ServiceNow-synchronisatieproblemen oplossen](platform/itsmc-resync-servicenow.md): nieuw artikel.
- [Bewaking van Azure- en Red Hat OpenShift v4-cluster beëindigen](insights/container-insights-optout-openshift-v4.md): nieuw artikel.
- [Bewaking van Azure Red Hat OpenShift v3-cluster beëindigen](insights/container-insights-optout-openshift-v3.md): nieuw artikel.
- [Bewaking van hybride Kubernetes-cluster beëindigen](insights/container-insights-optout-hybrid.md): nieuw artikel.

### <a name="insights"></a>Inzichten

- [Azure-sleutelkluizen bewaken met Azure Monitor voor sleutelkluizen (preview)](insights/key-vault-insights-overview.md): nieuw artikel.

### <a name="logs"></a>Logboeken

- [Servicelimieten van Azure Monitor](service-limits.md): bandbreedtebeperking voor gebruikersquery's toegevoegd.
- [Gebruik en kosten voor Azure Monitor-logboeken beheren](platform/manage-cost-storage.md): facturering voor logboekclusters toegevoegd. Kusto-query toegevoegd zodat klanten met verouderde per-node-prijscategorie kunnen bepalen of ze moeten overgaan naar een per-GB- of capaciteitsreserveringscategorie.

### <a name="metrics"></a>Metrische gegevens

- [Geavanceerde functies van Azure Metrics Explorer](platform/metrics-charts.md): sectie aggregatie toegevoegd.

### <a name="workbooks"></a>Werkmappen

- [Azure Monitor-workbooks en Azure Resource Manager-sjablonen](platform/workbooks-automate.md): Resource Manager-sjabloon toegevoegd voor implementeren van workbooksjabloon.

## <a name="march-2020"></a>Maart 2020

### <a name="general"></a>Algemeen

- [Overzicht Azure Monitor](overview.md): overzichtsvideo van Azure Monitor toegevoegd.
- [Door klant beheerde sleutelconfiguratie van Azure Monitor](platform/customer-managed-keys.md): algemene updates.
- [Azure Monitor-gegevensreferenties](/azure/azure-monitor/reference/): nieuwe site.

### <a name="alerts"></a>Waarschuwingen

- [Meldingen voor activiteitenlogboek maken, bekijken en beheren in Azure Monitor](platform/alerts-activity-log.md): aanvullende uitleg van Resource Manager-sjabloon.
- [Waarschuwingen voor metrische gegevens in Azure Monitor.](platform/alerts-metric-overview.md): bijgewerkt voor overheidsondersteuning.
- [Problemen oplossen met Azure Monitor-waarschuwingen en -meldingen](platform/alerts-troubleshoot.md): nieuw artikel.

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights automatiseren met PowerShell](app/powershell.md): ARMClient-voorbeelden toegevoegd.
- [Continue export van telemetrie van Application Insights](app/export-telemetry.md): tabel met informatie over exportstructuur toegevoegd.
- [Snapshot Debugger voor .NET-apps inschakelen in Azure App Service](app/snapshot-debugger-appservice.md): voorbeeld van Resource Manager-sjabloon toegevoegd.
- [Gebruik en kosten voor Azure Application Insights beheren](app/pricing.md): gegevens over waarschuwingen voor gegevenslimieten toegevoegd.
- [Python-toepassingen bewaken met Azure Monitor (preview)](app/opencensus-python.md): metrische standaardgegevens toegevoegd.
- [Ondersteuning voor brontoewijzing voor JavaScript-toepassinge, Azure Monitor Application Insights](app/source-map-support.md): nieuw artikel.

### <a name="containers"></a>Containers

- [Veelgestelde vragen over Azure Monitor](faq.md): update voor Azure Monitor voor containers.
- [GPU-bewaking configureren met Azure Monitor voor containers](insights/container-insights-gpu-monitoring.md): nieuw artikel.

### <a name="insights"></a>Inzichten

- [Beheeroplossing voor Office 365 in Azure](insights/solution-office-365.md): bijgewerkte datum van afschaffing.

### <a name="logs"></a>Logboeken

- [Logboekquery's optimaliseren in Azure Monitor](log-query/query-optimization.md): CPU-voorwaarde voor XML- en JSON-parsering toegevoegd.
- [Azure Log Analytics-werkruimte verwijderen en herstellen](platform/delete-workspace.md): probleemoplossing toegevoegd.
- [Azure Monitor-logboeken gebruiken met Azure Logic Apps en Power Automate](platform/logicapp-flow-connector.md): bijgewerkt voor nieuwe Azure Monitor-connector.

### <a name="metrics"></a>Metrische gegevens

- [Afschaffing van metrische schijfgegevens in de Azure-portal](platform/portal-disk-metrics-deprecation.md): nieuw artikel.
- [Zelfstudie: Een grafiek met metrische gegevens maken in Azure Monitor](learn/tutorial-metrics-explorer.md): video toegevoegd.

### <a name="platform-logs"></a>Platformlogboeken

- [Azure-activiteitenlogboek verzamelen en analyseren in Azure Monitor](platform/activity-log-collect.md): herschreven voor betere uitleg van verzamelen van activiteitenlogboek met diagnostische instellingen.

### <a name="virtual-machines"></a>Virtuele machines

- [Virtuele Azure-machines bewaken met Azure Monitor](insights/monitor-vm-azure.md): nieuw artikel.
- [Snelstart: Virtuele Azure-machines bewaken met Azure Monitor](learn/quick-monitor-azure-vm.md): bijgewerkt om Azure Monitor voor VM's toe te voegen.
- [Waarschuwingen van Azure Monitor voor VM's](insights/vminsights-alerts.md): nieuw artikel.
- [Overzicht Azure Monitor voor VM's inschakelen](insights/vminsights-enable-overview.md): downloadkoppelingen bijgewerkt voor agent.

Algemene updates voor algemene beschikbaarheid van Azure Monitor voor VM's

- [ Wat is Azure Monitor voor VM's?](insights/vminsights-overview.md)
- [Veelgestelde vragen over Azure Monitor voor VM's (GA)](insights/vminsights-ga-release-faq.md) 
- [Azure Monitor voor VM's inschakelen met Azure Policy](insights/vminsights-enable-at-scale-policy.md) 
- [Prestaties in kaart brengen met Azure Monitor voor VM's](insights/vminsights-performance.md)
- [Query's uitvoeren op logboeken van Azure Monitor voor VM's](insights/vminsights-log-search.md)
- [App-afhankelijkheden weergeven met Azure Monitor voor VM's](insights/vminsights-maps.md) 

### <a name="visualizations"></a>Visualisaties

- [Gegevens van Azure Monitor visualiseren](visualizations.md): bijgewerkt om geplande afschaffing van View Designer aan te geven.

## <a name="february-2020"></a>Februari 2020

### <a name="agents"></a>Agents

Meerdere updates als onderdeel van het herschrijven van inhoud van extensie voor diagnostische gegevens.

- [Overzicht van de Azure-bewakingsagents](platform/agents-overview.md): tabellen geherstructureerd voor verheldering van unieke eigenschappen van elke agent.
- [Overzicht Azure-extensie voor diagnostische gegevens](platform/diagnostics-extension-overview.md): volledig herschreven.
- [Blobopslag gebruiken voor IIS en tabelopslag voor gebeurtenissen in Azure Monitor](platform/diagnostics-extension-logs.md): algemeen aangepast voor update en helderheid.
- [Windows Azure-extensie voor diagnostische gegevens (WAD) installeren en configureren](platform/diagnostics-extension-windows-install.md): nieuw artikel. 
- [Schema van Windows diagnostische extensie](platform/diagnostics-extension-schema-windows.md): gereorganiseerd.
- [Gegevens verzenden van Windows Azure-extensie voor diagnostische gegevens naar Azure Event Hubs](platform/diagnostics-extension-stream-event-hubs.md): volledig herschreven en bijgewerkt.
- [Diagnostische gegevens opslaan en weergeven in Azure Storage](../cloud-services/diagnostics-extension-to-storage.md): volledig herschreven en bijgewerkt.
- [Logboekanalyse van extensie van virtuele machine voor Windows](../virtual-machines/extensions/oms-windows.md): verduidelijking relatie met Logboekanalyse-agent.
- [Azure Monitor-extensie van virtuele machine voor Linux](../virtual-machines/extensions/oms-linux.md): verduidelijking relatie met Logboekanalyse-agent.

### <a name="application-insights"></a>Application Insights

- [Verbindingsreeksen in Azure Application Insights](app/sdk-connection-string.md): nieuw artikel.

### <a name="insights-and-solutions"></a>Inzicht en oplossingen

#### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers

- [Azure Active Directory integreren met Azure Kubernetes Service](../aks/azure-ad-integration.md): opmerking toegevoegd voor het maken van een clienttoepassing om RBAC-enabled cluster te ondersteunen voor de ondersteuning van Azure Monitor voor containers.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines

- [Veelgestelde vragen over Azure Monitor voor VM's (GA)](insights/vminsights-ga-release-faq.md): wijziging in hoe prestatiegegevens worden opgeslagen.

#### <a name="office-365"></a>Office 365

- [Beheeroplossing voor Office 365 in Azure](insights/solution-office-365.md): bijgewerkte datum van afschaffing.


### <a name="logs"></a>Logboeken

- [Logboekquery's optimaliseren in Azure Monitor](log-query/query-optimization.md): nieuw artikel.
- [Gebruik en kosten beheren voor Azure Monitor-logboeken](platform/manage-cost-storage.md): verbeterde voorbeeldquery's voor beter begrip van uw gebruik.

### <a name="metrics"></a>Metrische gegevens

- [Metrische gegevens van Azure Monitor-platform exporteren via diagnostische instellingen](platform/metrics-supported-export-diagnostic-settings.md): sectie toegevoegd over wijziging van gedrag voor null en nulwaarden.

### <a name="visualizations"></a>Visualisaties

Meerdere nieuwe artikelen voor conversiegids View Designer naar werkmappen.

- [Overgangsgids voor Azure Monitor View Designer naar werkmappen](platform/view-designer-conversion-overview.md): nieuw artikel.
- [Opties voor conversie van Azure Monitor View Designer naar werkmappen](platform/view-designer-conversion-options.md): nieuw artikel.
- [Tegelconversies van Azure Monitor View Designer naar werkmappen](platform/view-designer-conversion-tiles.md): nieuw artikel.
- [Overzicht van en toegang tot conversie van Azure Monitor View Designer naar werkmappen](platform/view-designer-conversion-access.md): nieuw artikel.
- [Algemene taken voor conversie van Azure Monitor View Designer naar werkmappen](platform/view-designer-conversion-tasks.md): nieuw artikel.
- [Voorbeelden van conversie van Azure Monitor View Designer naar werkmappen](platform/view-designer-conversion-examples.md): nieuw artikel.

## <a name="january-2020"></a>Januari 2020

### <a name="general"></a>Algemeen

- [Wat wordt er bewaakt door Azure Monitor?](monitor-reference.md): nieuw artikel.

### <a name="agents"></a>Agents

- [Logboekgegevens verzamelen met Azure Log Analytics-agent](platform/log-analytics-agent.md): tabel met vereisten voor netwerkfirewall bijgewerkt.

### <a name="alerts"></a>Waarschuwingen

- [Actiegroepen maken en beheren in de Azure-portal](platform/action-groups.md): instelling verwijderd voor v2-functies die niet langer nodig is.
- [Een waarschuwing voor metrische gegevens maken met een Resource Manager-sjabloon](platform/alerts-metric-create-templates.md): voorbeeld voor de parameter *ignoreDataBefore* toegevoegd.  Beperkingen toegevoegd over regels met meerdere criteria.
- [Logboekanalysewaarschuwing REST API gebruiken](platform/api-alerts.md): JSON-voorbeeld gecorrigeerd.

### <a name="application-insights"></a>Application Insights

- [IP-adressen die worden gebruikt door Application Insights en logboekanalyse](app/ip-addresses.md): sectie beschikbaarheidstest bijgewerkt met hoe een regel moet worden toegevoegd voor binnenkomende poort, om verkeer toe te staan dat Azure Netwerkbeveiligingsgroepen gebruikt.
- [Problemen oplossen met Azure Application Insights Profiler](app/profiler-troubleshooting.md): algemene probleemoplossing bijgewerkt.
- [Steekproef nemen van telemetrie in Azure Application Insights](app/sampling.md): bijgewerkt en opnieuw gestructureerd om leesbaarheid te verbeteren op basis van klantenfeedback.

### <a name="data-security"></a>Gegevensbeveiliging

- [Door klant beheerde sleutelconfiguratie van Azure Monitor](platform/customer-managed-keys.md): nieuw artikel.

### <a name="insights-and-solutions"></a>Inzicht en oplossingen

#### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers

- [Gegevensverzameling van agents configureren voor Azure Monitor voor containers](insights/container-insights-agent-config.md): details toegevoegd voor upgrade van agent in Azure Red Hat OpenShift en extra informatie toegevoegd om onderscheid te maken tussen de methodes voor upgrade van agent.
- [Prestatiewaarschuwingen maken voor Azure Monitor voor containers](insights/container-insights-alerts.md): herziene informatie en bijgewerkte stappen voor het maken van een waarschuwing over prestatiegegevens die zijn opgeslagen in de werkruimte met werkruimte-contextwaarschuwingen.
- [Kubernetes bewaking met Azure Monitor voor containers](insights/container-insights-analyze.md): overzichts- en analyse-artikel bijgewerkt met betrekking tot Windows Kubernetes-clusters.
- [Azure Red Hat OpenShift-clusters configureren met Azure Monitor voor containers](insights/container-insights-azure-redhat-setup.md): details toegevoegd voor upgrade van agent in Azure Red Hat OpenShift en extra informatie toegevoegd om onderscheid te maken tussen de methodes voor upgrade van agent.
- [Hybride Kubernetes-clusters configureren met Azure Monitor voor containers](insights/container-insights-hybrid-setup.md): bijgewerkt om toegevoegde ondersteuning weer te geven voor beveiligde poort:10250 met de cAdvisor van Kubelet.
- [De agent van Azure Monitor voor containers beheren](insights/container-insights-manage-agent.md): details bijgewerkt met betrekking tot gedrag en configuratie van scraping van metrische gegevens met Azure Red Hat OpenShift vergeleken met andere typen Kubernetes-clusters.
- [Azure Monitor voor containers Prometheus-integratie configureren](insights/container-insights-prometheus-integration.md): details bijgewerkt met betrekking tot gedrag en configuratie van scraping van metrische gegevens met Azure Red Hat OpenShift vergeleken met andere typen Kubernetes-clusters.
- [Azure Monitor voor containers voor metrische gegevens bijwerken](insights/container-insights-update-metrics.md): details bijgewerkt met betrekking tot gedrag en configuratie van scraping van metrische gegevens met Azure Red Hat OpenShift vergeleken met andere typen Kubernetes-clusters.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines

- [Veelgestelde vragen over Azure Monitor voor VM's (GA)](insights/vminsights-ga-release-faq.md): informatie toegevoegd over upgrade van werkruimte en agents naar nieuwe versie.

#### <a name="office-365"></a>Office 365

- [Office 365-beheeroplossing in Azure](insights/solution-office-365.md): details en veelgestelde vragen toegevoegd over migratie naar Office 365-oplossing in Azure Sentinel. Sectie onboarding verwijderd.

### <a name="logs"></a>Logboeken

- [Log Analytics-werkruimten beheren in Azure Monitor](platform/manage-access.md): updates voor Niet-acties.
- [Gebruik en kosten voor Azure Monitor-logboeken beheren](platform/manage-cost-storage.md): verduidelijking toegevoegd van berekening van gegevensvolume in de sectie Prijsmodel.
- [Azure Resource Manager-sjablonen gebruiken om een logboekanalysewerkruimte te maken en te configureren](platform/template-workspace-configuration.md): sjabloon bijgewerkt met nieuwe prijscategorieën.

### <a name="platform-logs"></a>Platformlogboeken

- [Azure-activiteitenlogboek verzamelen met diagnostische instellingen, Azure Monitor](platform/diagnostic-settings-legacy.md): extra informatie over gewijzigde eigenschappen.
- [Azure-activiteitenlogboek exporteren](platform/activity-log-export.md): bijgewerkt voor gebruikersinterface. 

## <a name="december-2019"></a>December 2019

### <a name="agents"></a>Agents

- [Linux-computers verbinden met Azure Monitor](platform/agent-linux.md): nieuw artikel.

### <a name="alerts"></a>Waarschuwingen

- [Een waarschuwing voor metrische gegevens maken met een Resource Manager-sjabloon](platform/alerts-metric-create-templates.md): voorbeeld toegevoegd voor aangepaste metrische gegevens.
- [Waarschuwingen met dynamische drempelwaarden maken in Azure Monitor](platform/alerts-dynamic-thresholds.md): sectie toegevoegd over interpretatie van grafieken van dynamische drempels.
- [Overzicht van bewaken van waarschuwingen en meldingen in Azure](platform/alerts-overview.md): query bijgewerkt voor Resource Graph.
- [Ondersteunde resources voor metrische waarschuwingen in Azure Monitor](platform/alerts-metric-near-real-time.md): update voor ondersteunde metrische gegevens en dimensies.
- [Schakelen van verouderde Log Analytics-waarschuwings-API naar nieuwe Azure-waarschuwings-API](platform/alerts-log-api-switch.md): opmerking toegevoegd over aangepaste waarschuwingsnaam.
- [Begrijpen hoe waarschuwingen voor metrische gegevens werken in Azure Monitor](platform/alerts-metric-overview.md): ondersteunde resourcetypen voor bewaking op schaal toegevoegd.

### <a name="application-insights"></a>Application Insights

- [Application Insights voor Worker Service-apps (niet-HTTP-apps)](app/worker-service.md): standaardlogboekniveau toegevoegd aan C#-code. Versie van verwijzing van pakket bijgewerkt.
- [ApplicationInsights.config-referentie, Azure](app/configuration-with-applicationinsights-config.md): voorbeeldcode bijgewerkt.
- [Azure Application Insights bijwerken met PowerShell](app/powershell.md): update van Resource Manager-sjabloon.
- [Azure Monitor Application Insights NuGet-pakketten](app/nuget.md): bijgewerkte pakketversies.
- [Nieuwe Azure Application Insights-resource maken](app/create-new-resource.md): opmerking toegevoegd aan wereldwijde unieke naam.
- [Diagnose met Live Metrics Stream, Azure Application Insights](app/live-stream.md): versievereisten bijgewerkt voor ASP.NET Core SDK.
- [Gebeurtenisteller in Application Insights](app/eventcounters.md): categorie en tabel bijgewerkt voor customMetrics.
- [Java-traceringlogboeken verkennen in Azure Application Insights](app/java-trace-logs.md): configuratie toegevoegd voor logboekdrempel Java-agent.
- [IP-adressen gebruikt door Application Insights en logboekanalyse](app/ip-addresses.md): IP-adressen bijgewerkt voor Live Metrics Stream.
- [Prestaties van Azure-appservices bewaken](app/azure-web-apps.md): ondersteuning toegevoegd voor ASP.NET Core 3.0. 
- [Python-toepassingen met Azure Monitor bewaken (preview)](app/opencensus-python.md): verduidelijking toegevoegd voor OpenCensus Python schematoewijzing aan Azure .Monitor-schema.
- [Releaseopmerkingen voor Azure Application Insights](app/release-notes.md): opmerkingen toegevoegd voor oudere releases.
- [Telemetriekanalen in Azure Application Insights](app/telemetry-channels.md): duur bijgewerkt van genegeerde gegevens tijdens verlengde periode van verloren verbinding.
- [Telemetriesteekproeven in Azure Application Insights](app/sampling.md): codefragment gecorrigeerd voor aangepaste TelemetryInitializer.
- [Problemen oplossen met Application Insights in een Java-webproject](app/java-troubleshoot.md): instructie verwijderd over niet ondersteunen van afhankelijkheidsverzameling in JDK 9.

### <a name="insights-and-solutions"></a>Inzicht en oplossingen

- [Veelgestelde vragen over Azure Monitor voor containers](insights/container-insights-faq.md): vraag toegevoegd over velden installatiekopie en naam.
- [Azure SQL-analyseoplossing in Azure Monitor](insights/azure-sql.md): ondersteuning bijgewerkt voor database wacht op beheerd exemplaar.
- [Gegevensverzameling agents Azure Monitor voor containers configureren](insights/container-insights-agent-config.md): instelling toegevoegd voor enrich_container_logs.
- [Hybride Kubernetes-clusters configureren met Azure Monitor voor containers](insights/container-insights-hybrid-setup.md): sectie probleemoplossing toegevoegd.
- [Active Directory-replicatiestatus bewaken met Azure Monitor](insights/ad-replication-status.md): vereisten .NET Framework bijgewerkt.
- [Netwerkprestatiemeter-oplossing in Azure](insights/network-performance-monitor.md): ondersteunde regio's bijgewerkt.
- [Active Directory-omgeving optimaliseren met Azure Monitor](insights/ad-assessment.md): vereisten .NET Framework bijgewerkt.
- [SQL Server-omgeving optimaliseren met Azure Monitor](insights/sql-assessment.md): vereisten .NET Framework bijgewerkt.
- [System Center Operations Manager-omgeving optimaliseren met Azure Monitor](insights/scom-assessment.md): vereisten .NET Framework bijgewerkt.
- [Ondersteunde verbindingen met IT Service Management-connector in Azure-logboekanalyse](platform/itsmc-connections.md): New York toegevoegd aan vereist client-ID en clientgeheim.

### <a name="logs"></a>Logboeken

- [Azure Log Analytics-werkruimte verwijderen en herstellen](platform/delete-workspace.md): PowerShell-methode toegevoegd.
- [Azure Monitor-logboekimplementatie ontwerpen](platform/design-logs-deployment.md): opnamefrequentie van een werkruimte verhoogd.

### <a name="metrics"></a>Metrische gegevens

- [Metrische gegevens Azure Monitor-platform exporteren via diagnostische instellingen](platform/metrics-supported-export-diagnostic-settings.md): nieuw artikel.

### <a name="platform-logs"></a>Platformlogboeken

Meerdere artikelen bijgewerkt als onderdeel van herstructurering van platformlogboeken op basis van nieuwe functie voor configuratie van activiteitenlogboek met diagnostische instellingen.

- [Azure-resourcelogboeken archiveren naar opslagaccount](platform/resource-logs-collect-storage.md)
- [Azure-gebeurtenisschema in het activiteitenlogboek](platform/activity-log-schema.md)
- [Servicebeperkingen van Azure Monitor](service-limits.md)
- [Azure-activiteitenlogboeken verzamelen en analyseren in Log Analytics-werkruimte](platform/activity-log-collect.md)
- [Azure-activiteitenlogboek verzamelen met diagnostische instellingen (preview), Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Azure-activiteitenlogboeken verzamelen in een Log Analytics-werkruimte over Azure-tenants](platform/activity-log-collect-tenants.md)
- [Azure-resourcelogboeken verzamelen in Log Analytics-werkruimte](platform/resource-logs-collect-workspace.md)
- [Diagnostische instellingen in Azure maken met Resource Manager-sjabloon](platform/diagnostic-settings-template.md)
- [Diagnostische instelling maken die logboeken metrische gegevens verzamelt in Azure](platform/diagnostic-settings.md)
- [Azure-activiteitenlogboek exporteren](platform/activity-log-export.md)
- [Overzicht van Azure-platformlogboeken](platform/platform-logs-overview.md)
- [Bewakingsgegevens van Azure naar een event hub streamen](platform/stream-monitoring-data-event-hubs.md)
- [Azure-platformlogboeken naar een event hub streamen](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Snelstarts en zelfstudies

- [Een grafiek met metrische gegevens maken in Azure Monitor](learn/tutorial-metrics-explorer.md): nieuw artikel.
- [Resourcelogboeken verzamelen van een Azure-resource en analyseren met Azure Monitor](learn/tutorial-resource-logs.md): nieuw artikel.
- [Een Azure-resource bewaken met Azure Monitor](learn/quick-monitor-azure-resource.md): nieuw artikel.
   
## <a name="next-steps"></a>Volgende stappen

- Zie [Gids voor inzenders van documentatie](/contribute/) als u wilt bijdragen aan Azure Monitor-documentatie.
