---
title: Wat wordt gecontroleerd door Azure Monitor
description: Referentie van alle services en andere resources die worden gecontroleerd door Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: e0e98b87cf3612bf01f90f806ea64ef06d08c60a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255325"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Wat wordt gecontroleerd door Azure Monitor?
In dit artikel worden de verschillende toepassingen en services beschreven die worden gecontroleerd door Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Inzichten en kernoplossingen
Kerninzichten en oplossingen worden beschouwd als onderdeel van Azure Monitor en volgen de ondersteunings- en serviceniveauovereenkomsten voor Azure. Ze worden ondersteund in alle Azure-regio's waar Azure Monitor beschikbaar is.

### <a name="insights"></a>Inzichten

Inzichten bieden een op maat gemaakte monitoringervaring voor bepaalde toepassingen en services. Ze verzamelen en analyseren zowel logboeken als statistieken.

| Inzicht | Beschrijving |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Extensible Application Performance Management (APM) service om uw live webapplicatie op elk platform te monitoren. |
| [Azure-monitor voor containers](insights/container-insights-overview.md) | Bewaakt de prestaties van containerworkloads die zijn geïmplementeerd in Azure Container Instances of beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS). |
| [Azure Monitor voor Cosmos DB (voorbeeld)](insights/cosmosdb-insights-overview.md) | Biedt een overzicht van de algehele prestaties, fouten, capaciteit en operationele status van al uw Azure Cosmos DB-resources in een uniforme interactieve ervaring. |
| [Azure Monitor voor netwerken (voorbeeld)](insights/network-insights-overview.md) | Biedt een uitgebreid overzicht van de status en statistieken voor al uw netwerkbronnen. De geavanceerde zoekmogelijkheid helpt u bij het identificeren van resourceafhankelijkheden, waardoor scenario's zoals het identificeren van bronnen die uw website hosten, worden geïdentificeerd door simpelweg naar de naam van uw website te zoeken. |
[Azure-monitor voor resourcegroepen (voorbeeld)](insights/resource-group-insights.md) |  Triage en diagnose van eventuele problemen van uw individuele resources tegenkomen, terwijl het aanbieden van context met betrekking tot de gezondheid en prestaties van de resource groep als geheel. |
| [Azure Monitor voor opslag (voorbeeld)](insights/storage-insights-overview.md) | Biedt uitgebreide bewaking van uw Azure Storage-accounts door een uniforme weergave te bieden van de prestaties, capaciteit en beschikbaarheid van uw Azure Storage Services. |
| [Azure-monitor voor VM's](insights/container-insights-overview.md) | Controleert uw Virtuele Azure-machines (VM) en virtuele machineschaalsets op schaal. De service analyseert de prestaties en status van uw Windows- en Linux-VM's en bewaakt hun processen en afhankelijkheden van andere resources en externe processen. |

### <a name="core-solutions"></a>Kernoplossingen

Oplossingen zijn gebaseerd op logboekquery's en weergaven die zijn aangepast voor een bepaalde toepassing of service. Ze verzamelen en analyseren logs alleen en worden afgeschaft na verloop van tijd in het voordeel van inzichten.

| Oplossing | Beschrijving |
|:---|:---|
| [Agent gezondheid](insights/solution-agenthealth.md) | Analyseer de status en configuratie van Log Analytics-agents. |
| [Waarschuwingsbeheer](platform/alert-management-solution.md) | Analyseer waarschuwingen die zijn verzameld bij System Center Operations Manager, Nagios of Zabbix. |
| [Serviceoverzicht](insights/service-map.md) | Detecteert automatisch toepassingscomponenten op Windows- en Linux-systemen en brengt de communicatie tussen services in kaart. |



## <a name="azure-services"></a>Azure-services
In de volgende tabel worden Azure-services en de gegevens die ze verzamelen in Azure Monitor weergegeven. 

- Statistieken : de service verzamelt automatisch statistieken in Azure Monitor Metrics. 
- Logboeken : de service ondersteunt diagnostische instellingen die platformlogboeken en -statistieken kunnen verzamelen in Azure Monitor-logboeken.
- Insight - Er is een inzicht beschikbaar voor de service die een op maat gemaakte monitoring ervaring voor de service biedt.

| Service | Metrische gegevens | Logboeken | Inzicht | Opmerkingen |
|:---|:---|:---|:---|:---|
|Active Directory | Nee | Ja | [Ja](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory business-to-consumer | Nee | Nee | Nee |  |
|Active Directory Domain Services | Nee | Ja | Nee |  |
|Activiteitenlogboek | Nee | Ja | Nee | |
|Advanced Threat Protection | Nee | Nee | Nee |  |
|Advisor | Nee | Nee | Nee |  |
|AI Builder | Nee | Nee | Nee |  |
|Analysis Services | Ja | Ja | Nee |  |
|API for FHIR | Nee | Nee | Nee |  |
|API Management | Ja | Ja | Nee |  |
|App Service | Ja | Ja | Nee |  |
|AppConfig | Nee | Nee | Nee |  |
|Application Gateway | Ja | Ja | Nee |  |
|Attestation Service | Nee | Nee | Nee |  |
|Automation | Ja | Ja | Nee |  |
|Azure Service Manager (RDFE) | Nee | Nee | Nee |  |
|Backup | Nee | Ja | Nee |  |
|Bastion | Nee | Nee | Nee |  |
|Batch | Ja | Ja | Nee |  |
|Batch AI | Nee | Nee | Nee |  |
|Blockchain Service | Nee | Ja | Nee |  |
|Blueprints | Nee | Nee | Nee |  |
|Bot-service | Nee | Nee | Nee |  |
|Cloud Services | Ja | Ja | Nee | Agent die nodig is om gastbesturingssysteem en workflows te controleren.  |
|Cloud Shell | Nee | Nee | Nee |  |
|Cognitive Services | Ja | Ja | Nee |  |
|Container Instances | Ja | Nee | Nee |  |
|Container Registry | Ja | Ja | Nee |  |
|Content Delivery Network (CDN) | Nee | Ja | Nee |  |
|Cosmos DB | Ja | Ja | [Ja](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Nee | Nee | Nee |  |
|Data Box | Nee | Nee | Nee |  |
|Gegevenscatalogus Gen2 | Nee | Nee | Nee |  |
|Data Explorer | Ja | Ja | Nee |  |
|Data Factory | Ja | Ja | Nee |  |
|Gegevensfabriek v2 | Nee | Ja | Nee |  |
|Data Share | Nee | Nee | Nee |  |
|Database for MariaDB | Ja | Ja | Nee |  |
|Database for MySQL | Ja | Ja | Nee |  |
|Database for PostgreSQL | Ja | Ja | Nee |  |
|Databasemigratieservice | Nee | Nee | Nee |  |
|Databricks | Nee | Ja | Nee |  |
|DDoS Protection | Ja | Ja | Nee |  |
|DevOps | Nee | Nee | Nee |  |
|DNS | Ja | Nee | Nee |  |
|Domeinnamen | Nee | Nee | Nee |  |
|Dps | Nee | Nee | Nee |  |
|Dynamics 365 Klantbetrokkenheid | Nee | Nee | Nee |  |
|Dynamics 365 Financiën en Operaties | Nee | Nee | Nee |  |
|Event Grid | Ja | Nee | Nee |  |
|Event Hubs | Ja | Ja | Nee |  |
|ExpressRoute | Ja | Ja | Nee |  |
|Firewall | Ja | Ja | Nee |  |
|Front Door | Ja | Ja | Nee |  |
|Functions | Ja | Ja | Nee |  |
|HDInsight | Nee | Ja | Nee |  |
|HPC Cache | Nee | Nee | Nee |  |
|Gegevensbeveiliging | Nee | Ja | Nee |  |
|Intune | Nee | Ja | Nee |  |
|IoT Central | Nee | Nee | Nee |  |
|IoT Hub | Ja | Ja | Nee |  |
|Key Vault | Ja | Ja | Nee |  |
|Kubernetes Service (AKS) | Nee | Nee | [Ja](insights/container-insights-overview.md)  |  |
|Load Balancer | Ja | Ja | Nee |  |
|Logic Apps | Ja | Ja | Nee |  |
|Machine Learning-service | Nee | Nee | Nee |  |
|Managed Applications  | Nee | Nee | Nee |  |
|Kaarten  | Nee | Nee | Nee |  |
|Media Services | Ja | Ja | Nee |  |
|Microsoft Flow | Nee | Nee | Nee |  |
|Microsoft Managed Desktop | Nee | Nee | Nee |  |
|Microsoft PowerApps | Nee | Nee | Nee |  |
|Microsoft Social Engagement | Nee | Nee | Nee |  |
|Microsoft Stream | Ja | Ja | Nee |  |
|Migreren | Nee | Nee | Nee |  |
|Multi-Factor Authentication | Nee | Ja | Nee |  |
|Network Watcher | Ja | Ja | Nee |  |
|Notification Hubs | Ja | Nee | Nee |  |
|Open gegevenssets | Nee | Nee | Nee |  |
|Beleid | Nee | Nee | Nee |  |
|Power BI | Ja | Ja | Nee |  |
|Power BI Embedded | Nee | Nee | Nee |  |
|Private Link | Nee | Nee | Nee |  |
|Project Spool Communicatieplatform | Nee | Nee | Nee |  |
|Red Hat OpenShift | Nee | Nee | Nee |  |
|Redis Cache | Ja | Ja | Nee |  |
|Resource Graph | Nee | Nee | Nee |  |
|Resource Manager | Nee | Nee | Nee |  |
|Retail Search – door Bing | Nee | Nee | Nee |  |
|Search | Ja | Ja | Nee |  |
|Service Bus | Ja | Ja | Nee |  |
|Service Fabric | Nee | Ja | Nee | Agent die nodig is om gastbesturingssysteem en workflows te controleren.  |
|Aanmeldingsportal | Nee | Nee | Nee |  |
|Site Recovery | Nee | Ja | Nee |  |
|Spring Cloud-service | Nee | Nee | Nee |  |
|SQL Data Warehouse | Ja | Ja | Nee |  |
|SQL Database | Ja | Ja | Nee |  |
|SQL Server Stretch Database | Ja | Ja | Nee |  |
|Stack | Nee | Nee | Nee |  |
|Storage | Ja | Nee | [Ja](insights/storage-insights-overview.md) |  |
|Opslagcache | Nee | Nee | Nee |  |
|Opslagsynchronisatieservices | Nee | Nee | Nee |  |
|Stream Analytics | Ja | Ja | Nee |  |
|Time Series Insights | Ja | Ja | Nee |  |
|Tina | Nee | Nee | Nee |  |
|Traffic Manager | Ja | Ja | Nee |  |
|Universele afdruk | Nee | Nee | Nee |  |
|Virtual Machine Scale Sets | Nee | Ja | [Ja](insights/vminsights-overview.md) | Agent die nodig is om gastbesturingssysteem en workflows te controleren. |
|Virtuele machines | Ja | Ja | [Ja](insights/vminsights-overview.md) | Agent die nodig is om gastbesturingssysteem en workflows te controleren. |
|Virtual Network | Ja | Ja | [Ja](insights/network-insights-overview.md) |  |
|Virtueel netwerk - NSG-stroomlogboeken | Nee | Ja | Nee |  |
|VPN Gateway | Ja | Ja | Nee |  |
|Windows Virtual Desktop | Nee | Nee | Nee |  |


## <a name="product-integrations"></a>Productintegraties
De services en oplossingen in de volgende tabel slaan hun gegevens op in een Log Analytics-werkruimte, zodat ze kunnen worden geanalyseerd met andere logboekgegevens die door Azure Monitor zijn verzameld.

| Product/service | Beschrijving |
|:---|:---|
| [Azure Automation](/azure/automation/) | Beheer updates van het besturingssysteem en houd wijzigingen bij op Windows- en Linux-computers. Zie [Beheer van het bijhouden](../automation/change-tracking.md) en bijwerken [wijzigen](../automation/automation-update-management.md). |
| [Azure-informatiebeveiliging](https://docs.microsoft.com/azure/information-protection/) | Classificeren en optioneel beveiligen van documenten en e-mails. Zie [Centrale rapportage voor Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](/azure/security-center/) | Verzamel en analyseer beveiligingsgebeurtenissen en voer dreigingsanalyses uit. Zie [Gegevensverzameling in Azure Security Center](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | Maakt verbinding met verschillende bronnen, waaronder Office 365 en Amazon Web Services Cloud Trail. Zie [Gegevensbronnen verbinden](/azure/sentinel/connect-data-sources). |
| [Key Vault-analyse](insights/azure-key-vault.md) | Azure Key Vault AuditEvent-logboeken analyseren. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Maak een diagnostische instelling om logboeken naar Azure Monitor te verzenden. Zie [Logboekgegevens verzenden naar opslag, gebeurtenishubs of logboekanalyses in Intune (voorbeeld)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Netwerk  | [Netwerkprestatiemonitor](insights/network-performance-monitor.md) - Monitor netwerkconnectiviteit en -prestaties op service- en toepassingseindpunten.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) - Logboeken en statistieken analyseren vanuit Azure Application Gateway.<br>[Traffic Analytics](/azure/network-watcher/traffic-analytics) - Analyseert NSG-stroomlogboeken (Network Watcher Network Security Group) om inzicht te geven in de verkeersstroom in uw Azure-cloud. |
| [Office 365](insights/solution-office-365.md) | Houd uw Office 365-omgeving in de gaten. Bijgewerkte versie met verbeterde onboarding beschikbaar via Azure Sentinel. |
| [SQL Analytics](insights/azure-sql.md) | Controleer de prestaties van Azure SQL-databases, elastische pools en beheerde exemplaren op schaal en voor meerdere abonnementen. |
| [Surface Hub](insights/surface-hubs.md) | Houd de status en het gebruik van Surface Hub-apparaten bij. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Verzamel gegevens van Operations Manager-agents door hun beheergroep te verbinden met Azure Monitor. Zie [Operations Manager verbinden met Azure Monitor](platform/om-agents.md)<br> Beoordeel het risico en de status van uw beheergroep Van Operations Manager van het Systeemcentrum met [de oplossing van Operations Manager Assessment.](insights/scom-assessment.md) |
| [Microsoft Teams-kamers](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Geïntegreerd end-to-end beheer van Microsoft Teams Rooms-apparaten. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Bouw, test en distribueer toepassingen en controleer vervolgens hun status en gebruik. Zie [Start met het analyseren van uw mobiele app met App Center en Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Windows Update Compliance](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) - Beoordeel uw Windows-bureaubladupgrades.<br>[Desktop Analytics](https://docs.microsoft.com/configmgr/desktop-analytics/overview) - Integreert met Configuration Manager om inzicht en intelligentie te bieden om beter geïnformeerde beslissingen te nemen over de updatebereidheid van uw Windows-clients. |



## <a name="other-solutions"></a>Andere oplossingen
Er zijn andere oplossingen beschikbaar voor het bewaken van verschillende toepassingen en services, maar de actieve ontwikkeling is gestopt en is mogelijk niet in alle regio's beschikbaar. Ze vallen onder de azure log Analytics-overeenkomst op serviceniveau voor gegevensopname.

| Oplossing | Beschrijving |
|:---|:---|
| [Active Directory-statuscontrole](insights/ad-assessment.md) | Beoordeel het risico en de status van uw Active Directory-omgevingen. |
| [Active Directory-replicatiestatus](insights/ad-replication-status.md) | Controleert uw Active Directory-omgeving regelmatig op replicatiefouten. |
| [Analyse van activiteitenlogboeken](platform/activity-log-view.md#azure-portal) | Activiteitslogboekvermeldingen weergeven. |
| [DNS Analytics (voorbeeld)](insights/dns-analytics.md) | Verzamelt, analyseert en correleert Windows DNS analytische en auditlogs en andere gerelateerde gegevens van uw DNS-servers. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Verzamel, bekijk en analyseer uw cloud foundry-systeemstatus- en prestatiestatistieken voor meerdere implementaties. |
| [Containers](insights/containers.md) | Docker- en Windows-containerhosts weergeven en beheren. |
| [On-demand beoordelingen](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Beoordeel en optimaliseer de beschikbaarheid, beveiliging en prestaties van uw on-premises, hybride en cloud Microsoft-technologieomgevingen. |
| [SQL-statuscontrole](insights/sql-assessment.md) | Beoordeel het risico en de status van uw SQL Server-omgevingen.  |
| [Bedradingsgegevens](insights/wire-data.md) | Geconsolideerde netwerk- en prestatiegegevens die worden verzameld vanaf met Windows verbonden en met Linux verbonden computers met de Log Analytics-agent. |


## <a name="third-party-integration"></a>Integratie door derden

| Oplossing | Beschrijving |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Met de IT Service Management Connector (ITSMC) u Azure verbinden met een ondersteunde IT Service Management (ITSM) product/service.  |


## <a name="resources-outside-of-azure"></a>Bronnen buiten Azure
Azure Monitor kan gegevens verzamelen van bronnen buiten Azure met behulp van de methoden in de volgende tabel.

| Resource | Methode |
|:---|:---|
| Toepassingen | Webtoepassingen buiten Azure bewaken met behulp van Application Insights. Zie [Wat is Application Insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Virtuele machines | Gebruik de loganalytics-agent om gegevens te verzamelen van het gastbesturingssysteem van virtuele machines in andere cloudomgevingen of on-premises. Zie [Logboekgegevens verzamelen met de log-analyse-agent](platform/log-analytics-agent.md). |
| REST API-client | Er zijn afzonderlijke API's beschikbaar om gegevens te schrijven naar Azure Monitor-logboeken en -statistieken vanaf elke REST API-client. Zie [Logboekgegevens naar Azure Monitor verzenden met de HTTP Data Collector API](platform/data-collector-api.md) for Logs en Aangepaste statistieken voor een [Azure-bron verzenden naar de Azure Monitor-metrische opslag met behulp van een REST-API](platform/metrics-store-custom-rest-api.md) voor metrische gegevens. |



## <a name="next-steps"></a>Volgende stappen

- Lees meer over het [Azure Monitor-gegevensplatform dat de logboeken en statistieken opslaat die zijn verzameld door inzichten en oplossingen.](platform/data-platform.md)
- Voer een [zelfstudie in over het bewaken van een Azure-bron.](learn/tutorial-resource-logs.md)
- Voer een [zelfstudie in over het schrijven van een logboekquery om gegevens in Azure Monitor Logs te analyseren.](learn/tutorial-resource-logs.md)
- Voer een [zelfstudie in over het maken van een metrische grafiek om gegevens te analyseren in Azure Monitor Metrics](learn/tutorial-metrics-explorer.md).

 
