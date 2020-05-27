---
title: Inventarisatie van bewakings oplossingen in azure | Microsoft Docs
description: Bewakings oplossingen in Azure Monitor zijn een verzameling regels voor logica, visualisatie en gegevens aanschaf die gegevens over een bepaald probleem gebied hebben gedraaid.  Dit artikel bevat een lijst met beschik bare bewakings oplossingen van micro soft en informatie over de methode en frequentie van gegevens verzameling.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: b87179c79489bf781619b70b19ca8982f2e38dff
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816813"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Details van inventarisatie en gegevens verzameling voor het controleren van oplossingen in azure
[Bewakings oplossingen](solutions.md) gebruiken Services in azure om kennis te krijgen van de werking van een bepaalde toepassing of service. Bewakings oplossingen verzamelen doorgaans logboek gegevens en bieden query's en weer gaven om verzamelde gegevens te analyseren. U kunt bewakings oplossingen toevoegen aan Azure Monitor voor alle toepassingen en services die u gebruikt. Deze zijn doorgaans gratis beschikbaar, maar verzamelen gegevens die gebruiks kosten kunnen aanroepen.

Dit artikel bevat een lijst met beschik bare [bewakings oplossingen](solutions.md) van micro soft met koppelingen naar hun gedetailleerde documentatie.  Het bevat ook informatie over de methode en frequentie van het verzamelen van gegevens in Azure Monitor.  U kunt de informatie in dit artikel gebruiken om de verschillende beschik bare oplossingen te identificeren en inzicht te krijgen in de gegevens stroom en verbindings vereisten voor verschillende bewakings oplossingen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Lijst met bewakings oplossingen

De volgende tabel bevat de [bewakings oplossingen](solutions.md) in azure die door micro soft zijn verschaft. Een item in de kolom betekent dat met de oplossing gegevens worden verzameld in Azure Monitor met behulp van deze methode.  Als er voor een oplossing geen kolommen zijn geselecteerd, wordt deze rechtstreeks naar Azure Monitor geschreven vanuit een andere Azure-service. Volg de koppeling voor elk hiervan naar de gedetailleerde documentatie voor meer informatie.

Uitleg van de kolommen is als volgt:

- **Micro soft Monitoring Agent** : agent die wordt gebruikt in Windows en Linux om Management Packs uit te voeren van micro soft System Center-Operations Manager (om) en bewakings oplossingen van Azure. In deze configuratie wordt de agent rechtstreeks verbonden met Azure Monitor zonder dat er verbinding wordt gemaakt met een Operations Manager beheer groep. 
- **Operations Manager** -identieke agent als micro soft monitoring agent. In deze configuratie is het [verbonden met een Operations Manager-beheer groep](../platform/om-agents.md) die is verbonden met Azure monitor. 
-  **Azure Storage** -oplossing verzamelt gegevens van een Azure Storage-account. 
- **Operations Manager vereist?** -Een verbonden Operations Manager beheer groep is vereist voor het verzamelen van gegevens door de bewakings oplossing. 
- **Operations Manager agent gegevens die via de beheer groep worden verzonden** : als de agent is [verbonden met een om-beheer groep](../platform/om-agents.md), worden gegevens verzonden naar Azure monitor van de-beheer server. In dit geval hoeft de agent niet rechtstreeks verbinding te maken met Azure Monitor. Als dit selectie vakje niet is ingeschakeld, worden gegevens van de agent rechtstreeks naar Azure Monitor verzonden, zelfs als de agent is verbonden met een OM-beheer groep. Het moet kunnen communiceren met Azure Monitor via de [log Analytics gateway](../platform/gateway.md).
- **Verzamelings frequentie** : Hiermee geeft u de frequentie op waarmee gegevens worden verzameld door de bewakings oplossing. 



| **Bewakings oplossing** | **Platform** | **Micro soft Monitoring Agent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager vereist?** | **Operations Manager agent gegevens die via een beheer groep zijn verzonden** | **Verzamelingsfrequentie** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Activiteiten logboek analyse](../platform/activity-log-collect.md) | Azure | | | | | | bij melding |
| [AD-evaluatie](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagen |
| [AD-replicatiestatus](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dagen |
| [Status van agent](solution-agenthealth.md) | Windows en Linux | &#8226; | &#8226; | | | &#8226; | 1 minuut |
| [Waarschuwingenbeheer](../platform/alert-management-solution.md) (nagios) |Linux |&#8226; | | | | |bij aankomst |
| [Waarschuwingenbeheer](../platform/alert-management-solution.md) (zabbix) |Linux |&#8226; | | | | |1 minuut |
| [Waarschuwingenbeheer](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuten |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | N.v.t. |
| [Application Insights-connector (afgeschaft)](../platform/app-insights-connector.md) | Azure | | | |  |  | bij melding |
| [Automatisering - Hybrid Worker](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | N.v.t. |
| [Azure Application Gateway-analyse](azure-networking-analytics.md) | Azure |  |  |  |  |  | bij melding |
| **Bewakings oplossing** | **Platform** | **Micro soft Monitoring Agent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager vereist?** | **Operations Manager agent gegevens die via een beheer groep zijn verzonden** | **Verzamelingsfrequentie** |
| [Azure Analyse van netwerkbeveiligingsgroep (afgeschaft)](azure-networking-analytics.md) | Azure |  |  |  |  |  | bij melding |
| [Azure SQL-analyse (preview-versie)](azure-sql.md) | Windows | | | | | | 1 minuut |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | bij melding |
| [Capaciteit en prestaties (preview-versie)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |bij aankomst |
| [Tracering wijzigen](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[hangt](../../automation/change-tracking.md#change-tracking-and-inventory-data-collection) |
| [Tracering wijzigen](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[hangt](../../automation/change-tracking.md#change-tracking-and-inventory-data-collection) |
| [Containers](containers.md) | Windows en Linux | &#8226; | &#8226; |  |  |  | 3 minuten |
| [Key Vault-analyse](azure-key-vault.md) |Windows | | | | | |bij melding |
| [Malware-evaluatie](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |per uur |
| [Netwerkprestatiemeter](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP-handshake elke vijf seconden, gegevens verzonden om de drie minuten |
| [Office 365-analyse (preview-versie)](solution-office-365.md) |Windows | | | | | |bij melding |
| **Bewakings oplossing** | **Platform** | **Micro soft Monitoring Agent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager vereist?** | **Operations Manager agent gegevens die via een beheer groep zijn verzonden** | **Verzamelingsfrequentie** |
| [Service Fabric-analyse](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minuten |
| [Servicetoewijzing](service-map.md) | Windows en Linux | &#8226; | &#8226; |  |  |  | 15 seconden |
| [SQL-evaluatie](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagen |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |bij aankomst |
| [System Center Operations Manager-evaluatie (preview-versie)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | zeven dagen |
| [Updatebeheer](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |ten minste twee keer per dag en 15 minuten na het installeren van een update |
| [Gereedheid voor upgrade](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dagen |
| [VMWare-bewaking (afgeschaft)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuten |
| [Wire Data 2.0 (preview-versie)](wire-data.md) |Windows (2012 R2/8,1 of hoger) |&#8226; |&#8226; | | | | 1 minuut |




## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [installeren en gebruiken van bewakings oplossingen](solutions.md).
* Meer informatie over het [maken van query's](../log-query/log-query-overview.md) voor het analyseren van gegevens die zijn verzameld door bewakings oplossingen.
