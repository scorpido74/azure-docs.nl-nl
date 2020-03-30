---
title: Voorraad van bewakingsoplossingen in Azure | Microsoft Documenten
description: Monitoringoplossingen in Azure Monitor zijn een verzameling regels voor logica, visualisatie en gegevensverwerving die statistieken bieden die zijn gedraaid rond een bepaald probleemgebied.  In dit artikel vindt u een lijst met bewakingsoplossingen die beschikbaar zijn bij Microsoft en details over hun methode en frequentie van het verzamelen van gegevens.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 7b88d957bce45bf518fc77584f1691de8010459a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663126"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Gegevens over het verzamelen van gegevens voor bewakingsoplossingen in Azure
[Monitoringoplossingen](solutions.md) maken gebruik van services in Azure om extra inzicht te bieden in de werking van een bepaalde toepassing of service. Monitoringoplossingen verzamelen doorgaans logboekgegevens en bieden query's en weergaven om verzamelde gegevens te analyseren. U bewakingsoplossingen toevoegen aan Azure Monitor voor alle toepassingen en services die u gebruikt. Ze zijn meestal gratis beschikbaar, maar verzamelen gegevens die gebruikskosten kunnen inroepen.

Dit artikel bevat een lijst met [oplossingen](solutions.md) die beschikbaar zijn bij Microsoft met koppelingen naar hun gedetailleerde documentatie.  Het biedt ook informatie over hun methode en frequentie van het verzamelen van gegevens in Azure Monitor.  U de informatie in dit artikel gebruiken om de verschillende beschikbare oplossingen te identificeren en inzicht te krijgen in de gegevensstroom en verbindingsvereisten voor verschillende bewakingsoplossingen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Lijst van bewakingsoplossingen

In de volgende tabel worden de [bewakingsoplossingen](solutions.md) in Azure weergegeven die door Microsoft zijn geleverd. Een vermelding in de kolom betekent dat de oplossing gegevens verzamelt in Azure Monitor met die methode.  Als er geen kolommen zijn geselecteerd als er geen kolommen zijn geselecteerd, wordt deze rechtstreeks naar Azure Monitor geschreven vanuit een andere Azure-service. Volg de link voor elk van deze naar de gedetailleerde documentatie voor meer informatie.

De verklaringen van de kolommen zijn als volgt:

- **Microsoft-monitoringagent** - Agent die wordt gebruikt op Windows en Linux om beheerpakketten uit te voeren van SCOM en bewakingsoplossingen van Azure. In deze configuratie is de agent rechtstreeks verbonden met Azure Monitor zonder te zijn verbonden met een beheergroep operations manager. 
- **Operations Manager** - Identieke agent als Microsoft-bewakingsagent. In deze configuratie is deze [verbonden met een beheergroep Operations Manager](../platform/om-agents.md) die is verbonden met Azure Monitor. 
-  **Azure Storage** - Oplossing verzamelt gegevens van een Azure-opslagaccount. 
- **Operations Manager vereist?** - Er is een verbonden Operations Manager-beheergroep vereist voor het verzamelen van gegevens door de monitoringoplossing. 
- **Operations Manager-agentgegevens die via beheergroep worden verzonden** - Als de agent is [verbonden met een SCOM-beheergroep,](../platform/om-agents.md)worden gegevens vanaf de beheerserver naar Azure Monitor verzonden. In dit geval hoeft de agent geen rechtstreeks verbinding te maken met Azure Monitor. Als dit vak niet is geselecteerd, worden gegevens rechtstreeks van de agent naar Azure Monitor verzonden, zelfs als de agent is verbonden met een SCOM-beheergroep. Het moet kunnen communiceren met Azure Monitor via de [Log Analytics-gateway.](../platform/gateway.md)
- **Verzamelingsfrequentie** - Hiermee geeft u de frequentie op die gegevens worden verzameld door de bewakingsoplossing. 



| **Bewakingsoplossing** | **Platform** | **Microsoft-bewakingsagent** | **Operations Manager-agent** | **Azure-opslag** | **Operations Manager vereist?** | **Operations Manager agent gegevens verzonden via management groep** | **Verzamelingsfrequentie** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Analyse van activiteitenlogboeken](../platform/activity-log-collect.md) | Azure | | | | | | betreffende de kennisgeving |
| [AD-evaluatie](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagen |
| [AD-replicatiestatus](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dagen |
| [Status van agent](solution-agenthealth.md) | Windows en Linux | &#8226; | &#8226; | | | &#8226; | 1 minuut |
| [Alert Management](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |bij aankomst |
| [Alert management](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minuut |
| [Waarschuwingsbeheer](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuten |
| [Azure-siteherstel](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | N.v.t. |
| [Application Insights-connector (afgeschaft)](../platform/app-insights-connector.md) | Azure | | | |  |  | betreffende de kennisgeving |
| [Hybride medewerker automatisering](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | N.v.t. |
| [Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | betreffende de kennisgeving |
| **Bewakingsoplossing** | **Platform** | **Microsoft-bewakingsagent** | **Operations Manager-agent** | **Azure-opslag** | **Operations Manager vereist?** | **Operations Manager agent gegevens verzonden via management groep** | **Verzamelingsfrequentie** |
| [Azure Network Security Group Analytics (afgeschaft)](azure-networking-analytics.md) | Azure |  |  |  |  |  | betreffende de kennisgeving |
| [Azure SQL Analytics (Preview)](azure-sql.md) | Windows | | | | | | 1 minuut |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | betreffende de kennisgeving |
| [Capaciteit en prestaties (voorbeeld)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |bij aankomst |
| [Tracering wijzigen](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[Varieert](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Tracering wijzigen](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[Varieert](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Containers](containers.md) | Windows en Linux | &#8226; | &#8226; |  |  |  | 3 minuten |
| [Key Vault-analyse](azure-key-vault.md) |Windows | | | | | |betreffende de kennisgeving |
| [Malware-evaluatie](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |Uur |
| [Netwerkprestatiemeter](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP handshakes elke 5 seconden, gegevens verzonden om de 3 minuten |
| [Office 365 Analytics (voorbeeld)](solution-office-365.md) |Windows | | | | | |betreffende de kennisgeving |
| **Bewakingsoplossing** | **Platform** | **Microsoft-bewakingsagent** | **Operations Manager-agent** | **Azure-opslag** | **Operations Manager vereist?** | **Operations Manager agent gegevens verzonden via management groep** | **Verzamelingsfrequentie** |
| [Service Fabric-analyse](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minuten |
| [Serviceoverzicht](service-map.md) | Windows en Linux | &#8226; | &#8226; |  |  |  | 15 seconden |
| [SQL-evaluatie](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagen |
| [SurfaceHub (SurfaceHub)](surface-hubs.md) |Windows |&#8226; | | | | |bij aankomst |
| [Beoordeling van Operations Manager van het Systeemcentrum (voorbeeld)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | zeven dagen |
| [Updatebeheer](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |ten minste 2 keer per dag en 15 minuten na het installeren van een update |
| [Gereedheid voor upgrade](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dagen |
| [VMware Monitoring (afgeschaft)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuten |
| [Wire Data 2.0 (Voorbeeld)](wire-data.md) |Windows (2012 R2 / 8.1 of hoger) |&#8226; |&#8226; | | | | 1 minuut |




## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [installeren en gebruiken van bewakingsoplossingen.](solutions.md)
* Meer informatie over het [maken van query's](../log-query/log-query-overview.md) om gegevens te analyseren die worden verzameld door oplossingen te bewaken.
