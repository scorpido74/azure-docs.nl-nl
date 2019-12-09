---
title: Overzicht van Azure-platform logboeken | Microsoft Docs
description: Overzicht van Logboeken in Azure Monitor die uitgebreide, frequente gegevens bieden over de werking van een Azure-resource.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 480c028f11de9a7c44168b217ad3553d721d01e1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894553"
---
# <a name="overview-of-azure-platform-logs"></a>Overzicht van Azure platform-logboeken
Platform logboeken bieden gedetailleerde informatie over diagnostische gegevens en controle voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. Ze worden automatisch gegenereerd, maar u moet bepaalde platform logboeken configureren om te worden doorgestuurd naar een of meer doelen die moeten worden bewaard. Dit artikel bevat een overzicht van de platform logboeken, inclusief welke informatie ze bieden en hoe u deze kunt configureren voor het verzamelen en analyseren van gegevens.

## <a name="types-of-platform-logs"></a>Typen platform logboeken
De volgende tabel bevat de specifieke platform logboeken die beschikbaar zijn op verschillende lagen van Azure.

| Laag | Logboeken | Beschrijving |
|:---|:---|:---|
| Azure-bronnen | [Resource logboeken](resource-logs-overview.md) | Verstrek inzicht in de bewerkingen die zijn uitgevoerd in een Azure-resource (het *gegevens vlak*), bijvoorbeeld om een geheim te verkrijgen van een Key Vault of een aanvraag naar een Data Base te maken. De inhoud van bron Logboeken is afhankelijk van de Azure-service en het resource type.<br>*In de bron logboeken werden eerder Diagnostische logboeken genoemd.*  |
| Azure-abonnement | [Activiteitenlogboek](activity-logs-overview.md) | Biedt inzicht in de bewerkingen op elke Azure-resource in het abonnement van buiten (*het beheer vlak*) naast updates voor service Health gebeurtenissen. Er is één activiteiten logboek voor elk Azure-abonnement.   |
| Azure-tenant | [Azure Active Directory logboeken](../../active-directory/reports-monitoring/overview-reports.md)  | Bevat de geschiedenis van de aanmeldings activiteit en de audittrail van wijzigingen die zijn aangebracht in de Azure Active Directory voor een bepaalde Tenant.   |


![Overzicht van platformlogboeken](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>Platform logboeken weer geven
U kunt het [activiteiten logboek](activity-log-view.md) en [Azure Active Directory logboeken](../../active-directory/reports-monitoring/overview-reports.md) weer geven in de Azure Portal. U moet bron logboeken naar een [doel](#destinations) verzenden om ze te kunnen weer geven.


## <a name="destinations"></a>Bestemmingen
U kunt platform logboeken verzenden naar een of meer van de doelen in de volgende tabel, afhankelijk van uw bewakings vereisten. 

| Bestemming | Scenario | Naslaginformatie |
|:---|:---|:---|:---|
| Log Analytics-werkruimte | Analyseer de logboeken met andere bewakings gegevens en gebruik Azure Monitor functies, zoals logboek query's en waarschuwingen. | [Resource logboeken](resource-logs-collect-storage.md)<br>[Activiteitenlogboek](activity-log-collect.md)<br>[Azure activity Directory-logboeken](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Archiveer de logboeken voor controle, statische analyse of back-up. |[Resource logboeken](archive-diagnostic-logs.md)<br>[Activiteitenlogboek](activity-log-export.md)<br>[Azure activity Directory-logboeken](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Event Hub | De logboeken streamen naar logboek registraties en telemetrie-systemen van derden.  |[Resource logboeken](resource-logs-stream-event-hubs.md)<br>[Activiteitenlogboek](activity-log-export.md)<br>[Azure activity Directory-logboeken](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>Diagnostische instellingen en logboek profielen
Configureer doelen voor bron logboeken en Azure Active Directory logboeken door [een diagnostische instelling te maken](diagnostic-settings.md). Configureer bestemmingen voor het activiteiten logboek door [een logboek profiel te maken](activity-log-export.md) of door [het te verbinden met een log Analytics-werk ruimte](activity-log-collect.md).

De diagnostische instelling en het logboek profiel definiëren het volgende:

- Een of meer bestemmingen voor het verzenden van geselecteerde logboeken en metrische gegevens.
- Welke logboek categorieën en metrische gegevens van de resource naar de doelen worden verzonden.
- Als een opslag account is geselecteerd als doel, hoe lang elke logboek categorie moet worden bewaard.



## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het activiteiten logboek](activity-logs-overview.md)
* [Meer informatie over bron logboeken](resource-logs-overview.md)
* [Het resource logboek schema voor verschillende Azure-Services weer geven](diagnostic-logs-schema.md)
