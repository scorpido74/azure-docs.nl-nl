---
title: Gegevensbronnen in Azure Monitor | Microsoft Documenten
description: Beschrijft de beschikbare gegevens om de status en prestaties van uw Azure-resources en de toepassingen die erop worden uitgevoerd, te controleren.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: d1c0652844556b545cf0617032d21b80dd67d198
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479838"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Bronnen van bewakingsgegevens voor Azure Monitor
Azure Monitor is gebaseerd op een [veelvoorkomend monitoringgegevensplatform](data-platform.md) met [logboeken](data-platform-logs.md) en [metrische gegevens.](data-platform-metrics.md) Door gegevens in dit platform te verzamelen, kunnen gegevens uit meerdere bronnen samen worden geanalyseerd met behulp van een algemene set hulpprogramma's in Azure Monitor. Monitoringgegevens kunnen ook naar andere locaties worden verzonden om bepaalde scenario's te ondersteunen en sommige bronnen kunnen naar andere locaties schrijven voordat ze kunnen worden verzameld in Logboeken of Statistieken.

In dit artikel worden de verschillende bronnen van bewakingsgegevens beschreven die door Azure Monitor zijn verzameld, naast de bewakingsgegevens die zijn gemaakt door Azure-bronnen. Er worden koppelingen verstrekt naar gedetailleerde informatie over de configuratie die nodig is om deze gegevens naar verschillende locaties te verzamelen.

## <a name="application-tiers"></a>Toepassingslagen

Bronnen van bewakingsgegevens van Azure-toepassingen kunnen worden ingedeeld in lagen, waarbij de hoogste niveaus uw toepassing zelf zijn en de lagere lagen onderdelen van azure-platform zijn. De methode voor toegang tot gegevens van elke laag varieert. De toepassingslagen worden samengevat in de onderstaande tabel en de bronnen van bewakingsgegevens in elke laag worden weergegeven in de volgende secties. Zie [Gegevenslocaties in Azure bewaken](data-locations.md) voor een beschrijving van elke gegevenslocatie en hoe u toegang krijgen tot de gegevens.


![Controlelagen](../media/overview/overview.png)


### <a name="azure"></a>Azure
In de volgende tabel worden kort de toepassingslagen beschreven die specifiek zijn voor Azure. Volg de link voor meer informatie over elk in de onderstaande secties.

| Laag | Beschrijving | Verzamelingsmethode |
|:---|:---|:---|
| [Azure-tenant](#azure-tenant) | Gegevens over de werking van Azure-services op tenant-niveau, zoals Azure Active Directory. | Aad-gegevens weergeven in portal of verzameling configureren voor Azure Monitor met behulp van een tenantdiagnostische instelling. |
| [Azure-abonnement](#azure-subscription) | Gegevens met betrekking tot de status en het beheer van cross-resourceservices in uw Azure-abonnement, zoals Resource Manager en Servicestatus. | Bekijk in portal of configureer verzameling naar Azure Monitor met behulp van een logboekprofiel. |
| [Azure-resources](#azure-resources) |  Gegevens over de werking en prestaties van elke Azure-bron. | Statistieken die automatisch worden verzameld, weergeven in Metrics Explorer.<br>Diagnostische instellingen configureren om logboeken te verzamelen in Azure Monitor.<br>Monitoring oplossingen en inzichten beschikbaar voor meer gedetailleerde monitoring voor specifieke resource types. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, andere cloud of on-premises 
In de volgende tabel worden kort de toepassingslagen beschreven die zich mogelijk in Azure, een andere cloud of on-premises bevinden. Volg de link voor meer informatie over elk in de onderstaande secties.

| Laag | Beschrijving | Verzamelingsmethode |
|:---|:---|:---|
| [Besturingssysteem (gast)](#operating-system-guest) | Gegevens over het besturingssysteem op compute resources. | Installeer de medewerker Logboekanalyse om clientgegevensbronnen te verzamelen in azure-monitor- en afhankelijkheidsagent om afhankelijkheden te verzamelen die Azure Monitor voor VM's ondersteunen.<br>Installeer Azure Diagnostic Extension voor virtuele Azure-machines om logboeken en statistieken te verzamelen in Azure Monitor. |
| [Toepassingscode](#application-code) | Gegevens over de prestaties en functionaliteit van de werkelijke toepassing en code, inclusief prestatiesporen, toepassingslogboeken en gebruikerstelemetrie. | Instrumenter uw code om gegevens te verzamelen in Application Insights. |
| [Aangepaste bronnen](#custom-sources) | Gegevens van externe services of andere onderdelen of apparaten. | Verzamel logboek- of metrische gegevens in Azure Monitor van elke REST-client. |

## <a name="azure-tenant"></a>Azure-tenant
Telemetrie met betrekking tot uw Azure-tenant wordt verzameld van tenantbrede services zoals Azure Active Directory.

![Azure-tenantverzameling](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory-controlelogboeken
[Azure Active Directory-rapportage](../../active-directory/reports-monitoring/overview-reports.md) bevat de geschiedenis van aanmeldingsactiviteit en controlespoor van wijzigingen die binnen een bepaalde tenant zijn aangebracht. 

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure Monitor-logboeken | Configureer Azure AD-logboeken die moeten worden verzameld in Azure Monitor om ze te analyseren met andere bewakingsgegevens. | [Azure AD-logboeken integreren met Azure Monitor-logboeken (voorbeeld)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Azure AD-logboeken exporteren naar Azure Storage voor archivering. | [Zelfstudie: Azure AD-logboeken archiveren in een Azure-opslagaccount (preview)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Event Hub | Azure AD-logboeken streamen naar andere locaties met gebeurtenishubs. | [Zelfstudie: Azure Active Directory-logboeken streamen naar een Azure-gebeurtenishub (voorbeeld)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Azure-abonnement
Telemetrie met betrekking tot de status en werking van uw Azure-abonnement.

![Azure-abonnement](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure-activiteitenlogboek 
Het [Azure Activity-logboek](platform-logs-overview.md) bevat servicestatusrecords, samen met records voor eventuele configuratiewijzigingen die zijn aangebracht in de bronnen in uw Azure-abonnement. Het logboek Activiteit is beschikbaar voor alle Azure-bronnen en vertegenwoordigt hun _externe_ weergave.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|
| Activiteitenlogboek | Het activiteitenlogboek wordt verzameld in een eigen gegevensarchief dat u bekijken in het menu azure-monitor of gebruiken om waarschuwingen voor activiteitenlogboeken te maken. | [Het activiteitslogboek in de Azure-portal opvragen](activity-log-view.md#azure-portal) |
| Azure Monitor-logboeken | Configureer Azure Monitor Logs om het activiteitslogboek te verzamelen om het te analyseren met andere bewakingsgegevens. | [Azure-activiteitslogboeken verzamelen en analyseren in de werkruimte Log Analytics in Azure Monitor](activity-log-collect.md) |
| Azure Storage | Exporteer het logboek Activiteit naar Azure Storage voor archivering. | [Archiefactiviteitenlogboek](resource-logs-collect-storage.md)  |
| Event Hubs | Het activiteitenlogboek streamen naar andere locaties met gebeurtenishubs | [Logboek van streamactiviteit naar gebeurtenishub](resource-logs-stream-event-hubs.md). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) bevat informatie over de status van de Azure-services in uw abonnement waarop uw toepassing en resources vertrouwen.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Activiteitenlogboek<br>Azure Monitor-logboeken | Servicestatusrecords worden opgeslagen in het Azure Activity-logboek, zodat u ze bekijken in de Azure-portal of andere activiteiten uitvoeren die u uitvoeren met het activiteitenlogboek. | [Servicestatusmeldingen bekijken met Azure Portal](service-notifications.md) |


## <a name="azure-resources"></a>Azure-resources
Statistieken en bronlogboeken bieden informatie over de _interne_ werking van Azure-resources. Deze zijn beschikbaar voor de meeste Azure-services en monitoringoplossingen en -inzichten verzamelen aanvullende gegevens voor bepaalde services.

![Azure-bronverzameling](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Platformstatistieken 
De meeste Azure-services sturen [platformstatistieken](data-platform-metrics.md) die hun prestaties en werking rechtstreeks naar de metrische gegevensdatabase weergeven. De specifieke [statistieken variëren voor elk type resource](metrics-supported.md). 

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure-monitorstatistieken | Platformstatistieken schrijven naar de Azure Monitor metrics database zonder configuratie. Toegang tot platformstatistieken vanuit Metrics Explorer.  | [Aan de slag met Azure Metrics Explorer](metrics-getting-started.md)<br>[Ondersteunde statistieken met Azure Monitor](metrics-supported.md) |
| Azure Monitor-logboeken | Kopieer platformstatistieken naar logboeken voor trending en andere analyses met Behulp van Log Analytics. | [Azure-diagnostiek rechtstreeks naar Log Analytics](resource-logs-collect-workspace.md) |
| Event Hubs | Stream statistieken naar andere locaties met behulp van Gebeurtenishubs. |[Azure-bewakingsgegevens streamen naar een gebeurtenishub voor verbruik door een extern hulpprogramma](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Resourcelogboeken
[Resourcelogboeken](platform-logs-overview.md) bieden inzicht in de _interne_ werking van een Azure-bron.  Resourcelogboeken worden automatisch gemaakt, maar u moet een diagnostische instelling maken om een bestemming op te geven die voor elke resource moet worden verzameld.

De configuratievereisten en inhoud van resourcelogboeken verschillen per resourcetype en nog niet alle services maken deze. Zie [Ondersteunde services, schema's en categorieën voor Azure-bronlogboeken](diagnostic-logs-schema.md) voor meer informatie over elke service en koppelingen naar gedetailleerde configuratieprocedures. Als de service niet in dit artikel wordt weergegeven, maakt die service momenteel geen bronlogboeken.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure Monitor-logboeken | Stuur bronlogboeken naar Azure Monitor Logs voor analyse met andere verzamelde logboekgegevens. | [Azure-bronlogboeken verzamelen in de werkruimte Log Analytics in Azure Monitor](resource-logs-collect-storage.md) |
| Storage | Bronlogboeken verzenden naar Azure Storage voor archivering. | [Azure-bronlogboeken archiveren](resource-logs-collect-workspace.md) |
| Event Hubs | Stream bronlogboeken naar andere locaties met behulp van gebeurtenishubs. |[Azure-bronlogboeken streamen naar een gebeurtenishub](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Besturingssysteem (gast)
Compute resources in Azure, in andere clouds en on-premises hebben een gastbesturingssysteem om te controleren. Met de installatie van een of meer agents u telemetrie van de gast verzamelen in Azure Monitor om deze te analyseren met dezelfde bewakingshulpprogramma's als de Azure-services zelf.

![Azure compute resource collection](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure Diagnostische extensie
Als u de Azure Diagnostics-extensie voor Azure Virtual-machines inschakelt, u logboeken en statistieken verzamelen van het gastbesturingssysteem van Azure-compute resources, waaronder Azure Cloud Service (klassieke) web- en werknemersrollen, virtuele machines en virtuele machines schaalsets en Service Fabric.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Storage | Azure-diagnostische extensie schrijft altijd naar een Azure Storage-account. | [Windows Azure-diagnostische extensie (WAD) installeren en configureren](diagnostics-extension-windows-install.md)<br>[De diagnostische Linux-extensie gebruiken voor het bewaken van metrische gegevens en logboeken](../../virtual-machines/extensions/diagnostics-linux.md) |
| Azure-monitorstatistieken | Wanneer u de diagnostische extensie configureert om prestatiemeteritems te verzamelen, worden deze naar de Azure Monitor-metrische gegevensdatabase geschreven. | [Gastbesturingssysteemstatistieken naar de Azure Monitor-metrische winkel verzenden met behulp van een resourcemanagersjabloon voor een virtuele Windows-machine](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Event Hubs | Configureer de diagnostische extensie om de gegevens naar andere locaties te streamen met gebeurtenishubs.  | [Azure Diagnostics-gegevens streamen met gebeurtenishubs](diagnostics-extension-stream-event-hubs.md)<br>[De diagnostische Linux-extensie gebruiken voor het bewaken van metrische gegevens en logboeken](../../virtual-machines/extensions/diagnostics-linux.md) |
| Logboeken voor toepassingsinzichten | Verzamel logboeken en prestatiemeteritems van de compute resource die uw toepassing ondersteunt die moet worden geanalyseerd met andere toepassingsgegevens. | [Diagnostische gegevens over Cloud Service, Virtual Machine of Service Fabric naar Application Insights verzenden](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Log Analytics-agent 
Installeer de Log Analytics-agent voor uitgebreide monitoring en beheer van uw virtuele Windows- of Linux-machines. De virtuele machine kan worden uitgevoerd in Azure, een andere cloud of on-premises.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure Monitor-logboeken | De loganalytics-agent maakt rechtstreeks verbinding met Azure Monitor of via System Center Operations Manager en stelt u in staat gegevens te verzamelen uit gegevensbronnen die u configureert of van bewakingsoplossingen die aanvullende inzichten in toepassingen bieden draaien op de virtuele machine. | [Agentgegevensbronnen in Azure Monitor](agent-data-sources.md)<br>[Operations Manager verbinden met Azure Monitor](om-agents.md) |
| VM-opslag | Azure Monitor voor VM's gebruikt de agent Log Analytics om heidestatusgegevens op een aangepaste locatie op te slaan. Zie de volgende sectie voor meer informatie.  |


### <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines 
[Azure Monitor voor VM's](../insights/vminsights-overview.md) biedt een aangepaste bewakingservaring voor virtuele machines die functies bieden die verder gaan dan de belangrijkste Azure Monitor-functionaliteit. Het vereist een afhankelijkheidsagent op virtuele Windows- en Linux-machines die integreert met de Log Analytics-agent om ontdekte gegevens te verzamelen over processen die worden uitgevoerd op de virtuele machine en externe procesafhankelijkheden.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure Monitor-logboeken | Slaat gegevens op over processen en afhankelijkheden van de agent. | [Azure Monitor voor VM's (voorbeeld) Kaart gebruiken om toepassingsonderdelen te begrijpen](../insights/vminsights-maps.md) |



## <a name="application-code"></a>Toepassingscode
Gedetailleerde toepassingsbewaking in Azure Monitor wordt gedaan met [Application Insights,](https://docs.microsoft.com/azure/application-insights/) die gegevens verzamelt van toepassingen die op verschillende platforms worden uitgevoerd. De toepassing kan worden uitgevoerd in Azure, een andere cloud of on-premises.

![Verzameling van toepassingsgegevens](media/data-sources/applications.png)


### <a name="application-data"></a>Toepassingsgegevens
Wanneer u Application Insights inschakelt voor een toepassing door een instrumentatiepakket te installeren, verzamelt het statistieken en logboeken met betrekking tot de prestaties en werking van de toepassing. Application Insights slaat de gegevens die het verzamelt op in hetzelfde Azure Monitor-gegevensplatform dat wordt gebruikt door andere gegevensbronnen. Het bevat uitgebreide tools voor het analyseren van deze gegevens, maar u deze ook analyseren met gegevens uit andere bronnen met behulp van tools zoals Metrics Explorer en Log Analytics.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure Monitor-logboeken | Operationele gegevens over uw toepassing, waaronder paginaweergaven, toepassingsaanvragen, uitzonderingen en traceringen. | [Loggegevens analyseren in Azure Monitor](../log-query/log-query-overview.md) |
|                    | Afhankelijkheidsinformatie tussen toepassingscomponenten ter ondersteuning van toepassingskaart en telemetriecorrelatie. | [Telemetriecorrelatie in Application Insights](../app/correlation.md) <br> [Toepassingskaart](../app/app-map.md) |
|            | Resultaten van beschikbaarheidstests die de beschikbaarheid en responsiviteit van uw toepassing testen vanaf verschillende locaties op het openbare internet. | [De beschikbaarheid en reactiesnelheid van een website bewaken](../app/monitor-web-app-availability.md) |
| Azure-monitorstatistieken | Application Insights verzamelt statistieken die de prestaties en werking van de toepassing beschrijven, naast aangepaste statistieken die u in uw toepassing definieert in de Azure Monitor-metrische gegevensdatabase. | [Vooraf samengevoegde metrische gegevens op basis van logboeken in Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[Application Insights-API voor aangepaste gebeurtenissen en metrische gegevens](../app/api-custom-events-metrics.md) |
| Azure Storage | Stuur toepassingsgegevens naar Azure Storage voor archivering. | [Telemetrie exporteren vanuit Application Insights](../app/export-telemetry.md) |
|            | Details van beschikbaarheidstests worden opgeslagen in Azure Storage. Gebruik Toepassingsinzichten in de Azure-portal om te downloaden voor lokale analyse. Resultaten van beschikbaarheidstests worden opgeslagen in Azure Monitor-logboeken. | [De beschikbaarheid en reactiesnelheid van een website bewaken](../app/monitor-web-app-availability.md) |
|            | Traceertraceringsgegevens van Profiler worden opgeslagen in Azure Storage. Gebruik Toepassingsinzichten in de Azure-portal om te downloaden voor lokale analyse.  | [Productietoepassingen profileren in Azure met Toepassingsinzichten](../app/profiler-overview.md) 
|            | Foutopsporingsmomentopnamegegevens die zijn vastgelegd voor een subset van uitzonderingen, worden opgeslagen in Azure Storage. Gebruik Toepassingsinzichten in de Azure-portal om te downloaden voor lokale analyse.  | [Hoe momentopnamen werken](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Monitoring van oplossingen en inzichten
[Monitoringoplossingen](../insights/solutions.md) en [Insights](../insights/insights-overview.md) verzamelen gegevens om extra inzicht te geven in de werking van een bepaalde service of toepassing. Ze kunnen betrekking hebben op resources in verschillende toepassingslagen en zelfs meerdere lagen.

### <a name="monitoring-solutions"></a>Bewakingsoplossingen

| Doel | Beschrijving | Naslaginformatie
|:---|:---|:---|
| Azure Monitor-logboeken | Monitoringoplossingen verzamelen gegevens in Azure Monitor-logboeken waar deze kunnen worden geanalyseerd met behulp van de querytaal of [-weergaven](view-designer.md) die doorgaans in de oplossing zijn opgenomen. | [Gegevensverzamelingsgegevens voor bewakingsoplossingen in Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers
[Azure Monitor voor containers](../insights/container-insights-overview.md) biedt een aangepaste bewakingservaring voor [Azure Kubernetes Service (AKS).](/azure/aks/) Het verzamelt aanvullende gegevens over deze bronnen die in de volgende tabel worden beschreven.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure Monitor-logboeken | Slaat bewakingsgegevens op voor AKS, waaronder voorraad, logboeken en gebeurtenissen. Metrische gegevens worden ook opgeslagen in logboeken om de analysefunctionaliteit ervan in de portal te benutten. | [Inzicht in prestaties van een AKS-cluster met Azure Monitor voor containers](../insights/container-insights-analyze.md) |
| Azure-monitorstatistieken | Metrische gegevens worden opgeslagen in de metrische database om visualisatie en waarschuwingen te stimuleren. | [Containerstatistieken weergeven in metrics explorer](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Biedt directe toegang tot uw AKS-containerlogboeken (STDOut/STDerror), gebeurtenissen en podmetrics in de portal( AkS)-containerlogboeken (stdout/stderror). | [Kubernetes-logboeken, gebeurtenissen en podstatistieken in realtime bekijken](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines
[Azure Monitor voor VM's](../insights/vminsights-overview.md) biedt een aangepaste ervaring voor het bewaken van virtuele machines. Een beschrijving van de gegevens die door Azure Monitor voor VM's worden verzameld, is opgenomen in de sectie [Besturingssysteem (gast)](#operating-system-guest) hierboven.

## <a name="custom-sources"></a>Aangepaste bronnen
Naast de standaardlagen van een toepassing moet u mogelijk ook andere bronnen met telemetrie controleren die niet kunnen worden verzameld met de andere gegevensbronnen. Schrijf deze gegevens voor deze bronnen naar Metrische gegevens of logboeken met behulp van een Azure Monitor API.

![Aangepaste verzameling](media/data-sources/custom.png)

| Doel | Methode | Beschrijving | Naslaginformatie |
|:---|:---|:---|:---|
| Azure Monitor-logboeken | Gegevensverzamelaar-API | Verzamel logboekgegevens van elke REST-client en sla op in de werkruimte Log Analytics. | [Logboekgegevens naar Azure Monitor verzenden met de HTTP Data Collector API (public preview)](data-collector-api.md) |
| Azure-monitorstatistieken | API voor aangepaste statistieken | Verzamel metrische gegevens van elke REST-client en sla op in de Azure Monitor-metrische gegevensdatabase. | [Aangepaste statistieken voor een Azure-bron verzenden naar de Azure Monitor-metrische opslag met behulp van een REST-API](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Overige services
Andere services in Azure schrijven gegevens naar het Azure Monitor-gegevensplatform. Hiermee u gegevens die door deze services worden verzameld, analyseren met gegevens die door Azure Monitor zijn verzameld en gebruikmaken van dezelfde analyse- en visualisatietools.

| Service | Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Azure Monitor-logboeken | Azure Security Center slaat de beveiligingsgegevens op die het verzamelt in een Log Analytics-werkruimte waarmee deze kan worden geanalyseerd met andere logboekgegevens die door Azure Monitor zijn verzameld.  | [Gegevensverzameling in Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure Monitor-logboeken | Azure Sentinel slaat de gegevens die het verzamelt op uit verschillende gegevensbronnen op in een Log Analytics-werkruimte waarmee deze kan worden geanalyseerd met andere logboekgegevens die door Azure Monitor zijn verzameld.  | [Verbinding maken met gegevensbronnen](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [typen bewakingsgegevens die door Azure Monitor worden verzameld](data-platform.md) en hoe u deze gegevens bekijken en analyseren.
- Vermeld de [verschillende locaties waar Azure-bronnen gegevens opslaan](data-locations.md) en hoe u er toegang toe krijgen. 
