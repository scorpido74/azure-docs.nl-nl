---
title: Wat wordt bewaakt door Azure Monitor
description: Verwijzing van alle services en andere resources die worden bewaakt door Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/15/2020
ms.openlocfilehash: 146deba7a0ef1e0dc5ffe03f9ad414e752058274
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84945372"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Wat wordt er bewaakt door Azure Monitor?:
In dit artikel worden de verschillende toepassingen en services beschreven die door Azure Monitor worden bewaakt. 

## <a name="insights-and-core-solutions"></a>Inzichten en kern oplossingen
Kern inzichten en oplossingen worden beschouwd als onderdeel van Azure Monitor en volgen de ondersteuning en service overeenkomsten voor Azure. Ze worden ondersteund in alle Azure-regio's waar Azure Monitor beschikbaar is.

### <a name="insights"></a>Inzichten

Inzichten bieden een aangepaste bewakings ervaring voor bepaalde toepassingen en services. Ze verzamelen en analyseren beide logboeken en metrische gegevens.

| Inzicht | Beschrijving |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Uitbreid bare APM-service (Application Performance Management) voor het bewaken van uw Live Web-app op elk platform. |
| [Azure Monitor voor containers](insights/container-insights-overview.md) | Bewaakt de prestaties van container werkbelastingen die zijn geïmplementeerd op Azure Container Instances of beheerde Kubernetes-clusters die worden gehost op de Azure Kubernetes-service (AKS). |
| [Azure Monitor voor Cosmos DB](insights/cosmosdb-insights-overview.md) | Biedt een overzicht van de algemene prestaties, fouten, capaciteit en operationele status van al uw Azure Cosmos DB resources in een uniforme interactieve ervaring. |
| [Azure Monitor voor netwerken (preview-versie)](insights/network-insights-overview.md) | Biedt een uitgebreid overzicht van de status en metrische gegevens voor al uw netwerk bronnen. De geavanceerde zoek functie helpt u bij het identificeren van bron afhankelijkheden, het inschakelen van scenario's zoals het identificeren van resources die als host fungeren voor uw website, door eenvoudigweg te zoeken naar de naam van uw website. |
[Azure Monitor voor resource groepen (preview-versie)](insights/resource-group-insights.md) |  Sorteren en diagnose eventuele problemen die uw afzonderlijke bronnen ondervinden, terwijl u context biedt voor de status en prestaties van de resource groep als geheel. |
| [Azure Monitor voor opslag](insights/storage-insights-overview.md) | Biedt uitgebreide bewaking van uw Azure Storage-accounts door een uniforme weer gave te bieden van de prestaties, capaciteit en beschik baarheid van uw Azure Storage services. |
| [Azure Monitor voor virtuele machines](insights/vminsights-overview.md) | Bewaakt uw Azure virtual machines (VM) en virtuele-machine schaal sets op schaal. De service analyseert de prestaties en status van uw Windows- en Linux-VM's en bewaakt hun processen en afhankelijkheden van andere resources en externe processen. |
| [Azure Monitor voor Key Vault (preview-versie)](insights/key-vaults-insights-overview.md) | Povides uitgebreide bewaking van uw sleutel kluizen door een uniforme weer gave van uw Key Vault-aanvragen, prestaties, fouten en latentie te bieden. |
| [Azure Monitor voor Azure-cache voor redis (preview-versie)](insights/redis-cache-insights-overview.md) |  Biedt een geïntegreerde, interactieve weer gave van de algehele prestaties, fouten, capaciteit en operationele status. |


### <a name="core-solutions"></a>Kern oplossingen

Oplossingen zijn gebaseerd op logboek query's en weer gaven die zijn aangepast voor een bepaalde toepassing of service. Ze verzamelen en analyseren alleen logboeken en worden na verloop van tijd afgeschaft om inzicht te krijgen in de voor keuren.

| Oplossing | Beschrijving |
|:---|:---|
| [Status van agent](insights/solution-agenthealth.md) | Analyseer de status en configuratie van Log Analytics agents. |
| [Waarschuwingsbeheer](platform/alert-management-solution.md) | Analyseer waarschuwingen die zijn verzameld van System Center Operations Manager, nagios of zabbix. |
| [Servicetoewijzing](insights/service-map.md) | Detecteert automatisch toepassings onderdelen op Windows-en Linux-systemen en wijst de communicatie tussen services toe. |



## <a name="azure-services"></a>Azure-services
De volgende tabel geeft een lijst van Azure-Services en de gegevens die ze in Azure Monitor verzamelen. 

- Metrische gegevens: de service verzamelt automatisch metrische gegevens in Azure Monitor metrische gegevens. 
- Logboeken: de service ondersteunt Diagnostische instellingen waarmee de logboeken en metrische gegevens van het platform kunnen worden verzameld voor Azure Monitor Logboeken.
- Inzicht: er is een inzicht beschikbaar voor de service die een aangepaste bewakings ervaring biedt voor de service.

| Service | Metrische gegevens | Logboeken | Inzicht | Notities |
|:---|:---|:---|:---|:---|
|Active Directory | No | Ja | [Ja](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Nee | Nee | Nee |  |
|Active Directory Domain Services | No | Yes | No |  |
|Activiteitenlogboek | No | Yes | No | |
|Advanced Threat Protection | Nee | Nee | Nee |  |
|Advisor | Nee | Nee | Nee |  |
|AI Builder | Nee | Nee | Nee |  |
|Analysis Services | Yes | Ja | No |  |
|API for FHIR | Nee | Nee | Nee |  |
|API Management | Yes | Ja | No |  |
|App Service | Yes | Ja | No |  |
|AppConfig | Nee | Nee | Nee |  |
|Application Gateway | Yes | Ja | No |  |
|Attestation-service | Nee | Nee | Nee |  |
|Automation | Yes | Ja | No |  |
|Azure Service Manager (RDFE) | Nee | Nee | Nee |  |
|Backup | No | Yes | No |  |
|Bastion | Nee | Nee | Nee |  |
|Batch | Yes | Ja | No |  |
|Batch AI | Nee | Nee | Nee |  |
|Blockchain-service | No | Yes | No |  |
|Blueprints | Nee | Nee | Nee |  |
|Bot-service | Nee | Nee | Nee |  |
|Cloud Services | Yes | Ja | No | De agent die is vereist om het gast besturingssysteem en de werk stromen te bewaken.  |
|Cloud Shell | Nee | Nee | Nee |  |
|Cognitive Services | Yes | Ja | No |  |
|Container Instances | Yes | Nee | Nee |  |
|Container Registry | Yes | Ja | No |  |
|Content Delivery Network (CDN) | No | Yes | No |  |
|Cosmos DB | Yes | Ja | [Ja](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Nee | Nee | Nee |  |
|Data Box | Nee | Nee | Nee |  |
|Data Catalog Gen2 | Nee | Nee | Nee |  |
|Data Explorer | Yes | Ja | No |  |
|Data Factory | Yes | Ja | No |  |
|Data Factory v2 | No | Yes | No |  |
|Data Share | Nee | Nee | Nee |  |
|Database for MariaDB | Ja | Ja | No |  |
|Database for MySQL | Ja | Ja | No |  |
|Database for PostgreSQL | Ja | Ja | No |  |
|Database Migration Service | Nee | Nee | Nee |  |
|Databricks | No | Yes | No |  |
|DDoS Protection | Ja | Ja | No |  |
|DevOps | Nee | Nee | Nee |  |
|DNS | Yes | Nee | Nee |  |
|Domeinnamen | Nee | Nee | Nee |  |
|DPS | Nee | Nee | Nee |  |
|Dynamics 365-klant betrokkenheid | Nee | Nee | Nee |  |
|Dynamics 365-Financiën en-bewerkingen | Nee | Nee | Nee |  |
|Event Grid | Yes | Nee | Nee |  |
|Event Hubs | Ja | Ja | No |  |
|ExpressRoute | Ja | Ja | No |  |
|Firewall | Ja | Ja | No |  |
|Front Door | Ja | Ja | No |  |
|Functies | Ja | Ja | No |  |
|HDInsight | No | Yes | No |  |
|HPC Cache | Nee | Nee | Nee |  |
|Information Protection | No | Yes | No |  |
|Intune | No | Yes | No |  |
|IoT Central | Nee | Nee | Nee |  |
|IoT Hub | Ja | Ja | No |  |
|Key Vault | Ja | Ja | [Ja](insights/key-vaults-insights-overview.md) |  |
|Kubernetes Service (AKS) | No | Nee | [Ja](insights/container-insights-overview.md)  |  |
|Load Balancer | Yes | Ja | No |  |
|Logic Apps | Yes | Ja | No |  |
|Machine Learning Service | No | Nee | Nee |  |
|Managed Applications  | No | Nee | Nee |  |
|Maps  | No | Nee | Nee |  |
|Media Services | Yes | Ja | No |  |
|Microsoft Flow | No | Nee | Nee |  |
|Microsoft Managed Desktop | No | Nee | Nee |  |
|Microsoft PowerApps | No | Nee | Nee |  |
|Microsoft Social Engagement | No | Nee | Nee |  |
|Microsoft Stream | Yes | Ja | No |  |
|Migreren | No | Nee | Nee |  |
|Multi-Factor Authentication | No | Yes | No |  |
|Network Watcher | Yes | Ja | No |  |
|Notification Hubs | Yes | No | Nee |  |
|Open Datasets | No | Nee | Nee |  |
|Beleid | No | Nee | Nee |  |
|Power BI Embedded | Yes | Ja | No |  |
|Private Link | No | Nee | Nee |  |
|Communicatie platform voor project spooler | No | Nee | Nee |  |
|Red Hat OpenShift | No | Nee | Nee |  |
|Redis Cache | Yes | Ja | [Ja](insights/redis-cache-insights-overview.md) | |
|Resource Graph | No | Nee | Nee |  |
|Resource Manager | No | Nee | Nee |  |
|Retail-zoek opdracht: door Bing | Nee | Nee | Nee |  |
|Search | Yes | Ja | No |  |
|Service Bus | Yes | Ja | No |  |
|Service Fabric | No | Yes | No | De agent die is vereist om het gast besturingssysteem en de werk stromen te bewaken.  |
|Aanmeldings Portal | Nee | Nee | Nee |  |
|Siteherstel | No | Yes | No |  |
|Lente-Cloud service | Nee | Nee | Nee |  |
|SQL Data Warehouse | Yes | Ja | No |  |
|SQL Database | Yes | Ja | No |  |
|SQL Server Stretch Database | Yes | Ja | No |  |
|Stack | Nee | Nee | Nee |  |
|Storage | Yes | No | [Ja](insights/storage-insights-overview.md) |  |
|Opslag cache | Nee | Nee | Nee |  |
|Opslag synchronisatie Services | Nee | Nee | Nee |  |
|Stream Analytics | Yes | Ja | No |  |
|Time Series Insights | Yes | Ja | No |  |
|TINA | Nee | Nee | Nee |  |
|Traffic Manager | Yes | Ja | No |  |
|Universeel afdrukken | Nee | Nee | Nee |  |
|Virtuele-machineschaalsets | No | Ja | [Ja](insights/vminsights-overview.md) | De agent die is vereist om het gast besturingssysteem en de werk stromen te bewaken. |
|Virtual Machines | Yes | Ja | [Ja](insights/vminsights-overview.md) | De agent die is vereist om het gast besturingssysteem en de werk stromen te bewaken. |
|Virtual Network | Yes | Ja | [Ja](insights/network-insights-overview.md) |  |
|Virtual Network-NSG-stroom logboeken | No | Yes | No |  |
|VPN Gateway | Yes | Ja | No |  |
|Windows Virtual Desktop | Nee | Nee | Nee |  |


## <a name="product-integrations"></a>Product integraties
De services en oplossingen in de volgende tabel slaan hun gegevens op in een Log Analytics-werk ruimte, zodat deze kunnen worden geanalyseerd met andere logboek gegevens die worden verzameld door Azure Monitor.

| Product/service | Beschrijving |
|:---|:---|
| [Azure Automation](/azure/automation/) | Updates van het besturings systeem beheren en wijzigingen bijhouden op Windows-en Linux-computers. Zie [Wijzigingen bijhouden](../automation/change-tracking.md) en [updatebeheer](../automation/automation-update-management.md). |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) | U kunt documenten en e-mail berichten classificeren en optioneel beveiligen. Zie [centrale rapportage voor Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](/azure/security-center/) | Verzamelen en analyseren van beveiligings gebeurtenissen en het uitvoeren van bedreigings analyses. [Gegevens verzameling in azure Security Center](/azure/security-center/security-center-enable-data-collection) weer geven |
| [Azure Sentinel](/azure/sentinel/) | Maakt verbinding met verschillende bronnen, waaronder Office 365 en Amazon Web Services Cloud Trail. Zie [verbinding maken met gegevens bronnen](/azure/sentinel/connect-data-sources). |
| [Key Vault-analyse](insights/azure-key-vault.md) | Analyseer Azure Key Vault audit event-Logboeken. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Een diagnostische instelling maken om logboeken naar Azure Monitor te verzenden. Zie [logboek gegevens naar opslag, Event hubs of log Analytics verzenden in intune (preview)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Netwerk  | [Netwerkprestatiemeter](insights/network-performance-monitor.md) -Controleer de netwerk verbinding en prestaties voor service-en toepassings eindpunten.<br>[Azure-toepassing gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) -logboeken en metrische gegevens van Azure-toepassing gateway analyseren.<br>[Traffic Analytics](/azure/network-watcher/traffic-analytics) -Network Watcher netwerk beveiligings groep (NSG) stroom logboeken analyseren om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. |
| [Office 365](insights/solution-office-365.md) | Uw Office 365-omgeving bewaken. Bijgewerkte versie met verbeterde onboarding beschikbaar via Azure Sentinel. |
| [SQL Analytics](insights/azure-sql.md) | Bewaak de prestaties van Azure SQL-data bases, elastische Pools en beheerde exemplaren op schaal en op meerdere abonnementen. |
| [Surface Hub](insights/surface-hubs.md) | De status en het gebruik van Surface Hub apparaten bijhouden. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Gegevens verzamelen van Operations Manager agents door hun beheer groep te verbinden met Azure Monitor. Zie [Operations Manager verbinding maken met Azure monitor](platform/om-agents.md)<br> Evalueer het risico en de status van uw System Center Operations Manager-beheer groep met [Operations Manager-beoordelings](insights/scom-assessment.md) oplossing. |
| [Micro soft teams-kamers](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Geïntegreerd, end-to-end-beheer van micro soft teams-apparaten. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Bouw, test en distribueer toepassingen en controleer hun status en gebruik. Zie [beginnen met het analyseren van uw mobiele app met app Center en Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Windows Update naleving](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) : Evalueer de upgrades van uw Windows-bureau blad.<br>[Desktop Analytics](https://docs.microsoft.com/configmgr/desktop-analytics/overview) : integreert met Configuration Manager om inzicht en intelligentie te bieden, zodat u meer weloverwogen beslissingen kunt nemen over de update gereedheids van uw Windows-clients. |



## <a name="other-solutions"></a>Andere oplossingen
Andere oplossingen zijn beschikbaar voor het bewaken van verschillende toepassingen en services, maar actieve ontwikkeling is gestopt en is mogelijk niet beschikbaar in alle regio's. Deze worden gedekt door de Azure Log Analytics gegevens opname service level agreement.

| Oplossing | Beschrijving |
|:---|:---|
| [Active Directory status controle](insights/ad-assessment.md) | Het risico en de status van uw Active Directory omgevingen evalueren. |
| [Replicatie status van Active Directory](insights/ad-replication-status.md) | Bewaakt uw Active Directory-omgeving regel matig voor replicatie fouten. |
| [Activiteiten logboek analyse](platform/activity-log.md#activity-log-analytics-monitoring-solution) | Vermeldingen in het activiteiten logboek weer geven. |
| [DNS-analyse (preview-versie)](insights/dns-analytics.md) | Verzamelt, analyseert en correleert Windows DNS analytic-en audit logboeken en andere gerelateerde gegevens van uw DNS-servers. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Verzamel, Bekijk en analyseer uw Cloud Foundry systeem status-en prestatie gegevens over meerdere implementaties. |
| [Containers](insights/containers.md) | Docker-en Windows-container-hosts weer geven en beheren. |
| [Evaluaties op aanvraag](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | De beschik baarheid, de beveiliging en de prestaties van uw on-premises, hybride en Cloud micro soft-technologie omgevingen beoordelen en optimaliseren. |
| [SQL-status controle](insights/sql-assessment.md) | Het risico en de status van uw SQL Server omgevingen evalueren.  |
| [Bedradingsgegevens](insights/wire-data.md) | Geconsolideerde netwerk-en prestatie gegevens die zijn verzameld van met Windows verbonden en Linux verbonden computers met de Log Analytics-agent. |

## <a name="third-party-integration"></a>Integratie van derden

| Oplossing | Beschrijving |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Met de IT Service Management-connector (ITSMC) kunt u Azure verbinden met een ondersteund ITSM-product/service (IT-servicebeheer).  |


## <a name="resources-outside-of-azure"></a>Bronnen buiten Azure
Azure Monitor kunt gegevens verzamelen van resources buiten Azure met behulp van de methoden die in de volgende tabel worden weer gegeven.

| Resource | Methode |
|:---|:---|
| Toepassingen | Bewaak webtoepassingen buiten Azure met Application Insights. Zie [Wat is Application Insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Virtuele machines | Gebruik de Log Analytics-agent voor het verzamelen van gegevens van het gast besturingssysteem van virtuele machines in andere Cloud omgevingen of on-premises. Zie [logboek gegevens verzamelen met de log Analytics-agent](platform/log-analytics-agent.md). |
| REST API-client | Er zijn afzonderlijke Api's beschikbaar voor het schrijven van gegevens naar Azure Monitor logboeken en meet waarden van een REST API-client. Zie [logboek gegevens naar Azure monitor verzenden met de http data collector-API](platform/data-collector-api.md) voor logboeken en [aangepaste metrische gegevens voor een Azure-resource naar het Azure monitor metrische archief verzenden met behulp van een rest API](platform/metrics-store-custom-rest-api.md) voor metrieken. |



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Azure monitor-gegevens platform waarin de logboeken en gegevens worden opgeslagen die door inzichten en oplossingen zijn verzameld](platform/data-platform.md).
- Voltooi een [zelf studie over het bewaken van een Azure-resource](learn/tutorial-resource-logs.md).
- Voltooi een [zelf studie over het schrijven van een logboek query voor het analyseren van gegevens in azure monitor logboeken](learn/tutorial-resource-logs.md).
- Voltooi een [zelf studie over het maken van een metrische grafiek voor het analyseren van gegevens in azure monitor meet waarden](learn/tutorial-metrics-explorer.md).

 
