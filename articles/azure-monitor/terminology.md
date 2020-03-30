---
title: Updates voor azure monitor-terminologie | Microsoft Documenten
description: Beschrijft recente wijzigingen in de terminologie die zijn aangebracht in Azure-bewakingsservices.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 0c5e1096b0780d2f9e50389f772e63344935012b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274136"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor-naamgeving en terminologiewijzigingen
Er zijn onlangs belangrijke wijzigingen aangebracht in Azure Monitor, waarbij verschillende services zijn geconsolideerd om de bewaking voor Azure-klanten te vereenvoudigen. In dit artikel worden recente naamswijzigingen en terminologiewijzigingen in azure monitor-documentatie beschreven.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Oktober 2019 - Diagnostisch logboek voor bronlogboek
"Diagnostische logs" werden gewijzigd in "resource logs" om beter overeenkomen met wat er daadwerkelijk wordt verzameld. De term "diagnostische instellingen" blijft hetzelfde.  

## <a name="february-2019---log-analytics-terminology"></a>Februari 2019 - Log Analytics terminologie
Na de consolidatie van verschillende services onder Azure Monitor, zetten we de volgende stap door terminologie in onze documentatie aan te passen om de Azure Monitor-service en de verschillende componenten beter te beschrijven. 

### <a name="log-analytics"></a>Log Analytics
Azure Monitor-logboekgegevens worden nog steeds opgeslagen in een Log Analytics-werkruimte en worden nog steeds verzameld en geanalyseerd door dezelfde Log Analytics-service, maar we wijzigen de term _Log Analytics_ op veel plaatsen in _Azure Monitor-logboeken._ Deze term weerspiegelt beter zijn rol in Azure Monitor en biedt een betere consistentie met [statistieken in Azure Monitor.](platform/data-platform-metrics.md)

De term _log analytics_ is nu vooral van toepassing op de pagina in de Azure-portal die wordt gebruikt om query's te schrijven en uit te voeren en logboekgegevens te analyseren. Het is het functionele equivalent van [metrics explorer](platform/metrics-charts.md), dat is de pagina in de Azure-portal die wordt gebruikt om metrische gegevens te analyseren.

### <a name="log-analytics-workspaces"></a>Log Analytics-werkruimten
[Werkruimten](platform/manage-access.md) die logboekgegevens in Azure Monitor bevatten, worden nog steeds Log Analytics-werkruimten genoemd. Het menu **Logboekanalyse** in de Azure-portal is omgedoopt tot **Log Analytics-werkruimten** en is het de plek waar u [nieuwe werkruimten maakt](learn/quick-create-workspace.md) en gegevensbronnen configureert. Analyseer uw logboeken en andere bewakingsgegevens in **Azure Monitor** en configureer uw werkruimte in **Logboekanalyse-werkruimten.**

### <a name="management-solutions"></a>Beheeroplossingen
[Managementoplossingen](insights/solutions.md) zijn omgedoopt tot _monitoringoplossingen,_ die hun functionaliteit beter beschrijven.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Augustus 2018 - Consolidatie van bewakingsservices in Azure Monitor
Log Analytics en Application Insights zijn geconsolideerd in Azure Monitor om één geïntegreerde ervaring te bieden voor het bewaken van Azure-resources en hybride omgevingen. Er is geen functionaliteit verwijderd uit deze services en gebruikers kunnen dezelfde scenario's uitvoeren die ze altijd hebben voltooid zonder verlies of compromis van functies.

Documentatie voor elk van deze services is geconsolideerd in één set inhoud voor Azure Monitor. Dit zal de lezer helpen bij het vinden van alle inhoud voor een bepaald monitoringscenario op één locatie, in tegenstelling tot het moeten verwijzen naar meerdere sets inhoud. Naarmate de geconsolideerde service evolueert, zal de inhoud meer geïntegreerd worden.

Andere functies die als onderdeel van Log Analytics werden beschouwd, zoals agents en weergaven, zijn ook verplaatst als functies van Azure Monitor. Hun functionaliteit is niet veranderd, behalve mogelijke verbeteringen aan hun ervaring in de Azure-portal.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>April 2018 - Pensioen van het merk Operations Management Suite
Operations Management Suite (OMS) was een bundeling van de volgende Azure-beheerservices voor licentiedoeleinden:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Er zijn nieuwe prijzen ingevoerd voor deze diensten](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)en de OMS-bundeling is niet langer beschikbaar voor nieuwe klanten. Geen van de services die deel uitmaakten van OMS is gewijzigd, behalve de consolidatie in Azure Monitor hierboven beschreven. 




## <a name="next-steps"></a>Volgende stappen

- Lees een [overzicht van Azure Monitor](overview.md) waarin de verschillende componenten en functies worden beschreven.
- Meer informatie over de [overgang van de OMS-portal](../log-analytics/log-analytics-oms-portal-transition.md).
