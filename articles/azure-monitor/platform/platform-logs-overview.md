---
title: Overzicht van Azure-platform logboeken | Microsoft Docs
description: Overzicht van Logboeken in Azure Monitor die uitgebreide, frequente gegevens bieden over de werking van een Azure-resource.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659317"
---
# <a name="overview-of-azure-platform-logs"></a>Overzicht van Azure platform-logboeken
Platform logboeken bieden gedetailleerde informatie over diagnostische gegevens en controle voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. Ze worden automatisch gegenereerd, maar u moet bepaalde platform logboeken configureren om te worden doorgestuurd naar een of meer doelen die moeten worden bewaard. Dit artikel bevat een overzicht van de platform logboeken, inclusief welke informatie ze bieden en hoe u deze kunt configureren voor het verzamelen en analyseren van gegevens.

## <a name="types-of-platform-logs"></a>Typen platform logboeken
De volgende tabel bevat de specifieke platform logboeken die beschikbaar zijn op verschillende lagen van Azure.

| Logboek | Laag | Beschrijving |
|:---|:---|:---|
| Resourcelogboeken | Azure-bronnen | Verstrek inzicht in de bewerkingen die zijn uitgevoerd in een Azure-resource (het *gegevens vlak*), bijvoorbeeld om een geheim te verkrijgen van een Key Vault of een aanvraag naar een Data Base te maken. De inhoud van bron Logboeken is afhankelijk van de Azure-service en het resource type.<br><br>*In de bron logboeken werden eerder Diagnostische logboeken genoemd.*  |
| Activiteitenlogboek | Azure-abonnement | Biedt inzicht in de bewerkingen op elke Azure-resource in het abonnement van buiten (*het beheer vlak*) naast updates voor service Health gebeurtenissen. Gebruik het activiteiten logboek om te bepalen _wat_, _wie_en _Wanneer_ voor schrijf bewerkingen (put, post, Delete) die zijn uitgevoerd op de resources in uw abonnement. U kunt ook de status van de bewerking en andere relevante eigenschappen begrijpen.  Er is één activiteiten logboek voor elk Azure-abonnement. |
| Azure Active Directory logboeken | Azure-Tenant |  Bevat de geschiedenis van de aanmeldings activiteit en de audittrail van wijzigingen die zijn aangebracht in de Azure Active Directory voor een bepaalde Tenant. Zie [Wat zijn Azure Active Directory rapporten?](../../active-directory/reports-monitoring/overview-reports.md) voor een volledige beschrijving van Azure Active Directory Logboeken.   |

> [!NOTE]
> Het Azure-activiteiten logboek is voornamelijk bedoeld voor activiteiten die zich in Azure Resource Manager voordoen. Er worden geen resources bijgehouden met het klassieke/RDFE-model. Sommige klassieke resource typen hebben een proxy resource provider in Azure Resource Manager (bijvoorbeeld micro soft. ClassicCompute). Als u met een klassiek resource type communiceert via Azure Resource Manager met behulp van deze proxy resource providers, worden de bewerkingen weer gegeven in het activiteiten logboek. Als u communiceert met een klassiek resource type buiten de Azure Resource Manager-proxy's, worden uw acties alleen vastgelegd in het bewerkings logboek. Het bewerkings logboek kan worden doorzocht in een afzonderlijk gedeelte van de portal.

![Overzicht van platformlogboeken](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Platform logboeken weer geven
Er zijn verschillende opties voor het weer geven en analyseren van de verschillende logboeken van het Azure-platform.

- Bekijk het activiteiten logboek in de Azure Portal en open gebeurtenissen vanuit Power shell en CLI. Zie [activiteiten logboek gebeurtenissen voor Azure weer geven en ophalen](activity-log-view.md) voor meer informatie. 
- Azure Active Directory beveiligings-en activiteiten rapporten weer geven in de Azure Portal. Zie [Wat zijn Azure Active Directory-rapporten?](../../active-directory/reports-monitoring/overview-reports.md)  voor meer informatie.
- Resource logboeken worden automatisch gegenereerd door ondersteunde Azure-resources, maar ze kunnen niet worden weer gegeven, tenzij u ze naar een [bestemming](#destinations)verzendt. 

## <a name="destinations"></a>Ontvangst
U kunt platform logboeken verzenden naar een of meer van de doelen in de volgende tabel, afhankelijk van uw bewakings vereisten. Configureer doelen voor platform logboeken door [een diagnostische instelling te maken](diagnostic-settings.md).

| Doel | Scenario | Verwijzingen |
|:---|:---|:---|:---|
| Log Analytics-werkruimte | Analyseer de logboeken met andere bewakings gegevens en gebruik Azure Monitor functies, zoals logboek query's en waarschuwingen. | [Activiteiten logboek en resource logboeken](resource-logs-collect-workspace.md)<br>[Azure activity Directory-logboeken](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure-opslag | Archiveer de logboeken voor controle, statische analyse of back-up. |[Activiteiten logboek en resource logboeken](archive-diagnostic-logs.md)<br>[Azure activity Directory-logboeken](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Event Hub | De logboeken streamen naar logboek registraties en telemetrie-systemen van derden.  |[Activiteiten logboek en resource logboeken](resource-logs-stream-event-hubs.md)<br>[Azure activity Directory-logboeken](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Volgende stappen

* [Het schema van het activiteiten logboek voor verschillende categorieën weer geven](activity-log-schema.md)
* [Het resource logboek schema voor verschillende Azure-Services weer geven](diagnostic-logs-schema.md)
