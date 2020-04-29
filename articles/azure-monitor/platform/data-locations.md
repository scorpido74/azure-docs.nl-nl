---
title: Gegevens locaties bewaken in Azure Monitor | Microsoft Docs
description: Hierin worden de verschillende locaties beschreven waar bewakings gegevens worden opgeslagen in azure, met inbegrip van het Azure Monitor-gegevens platform.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77666612"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Gegevens locaties bewaken in Azure Monitor

Azure Monitor is gebaseerd op een [gegevens platform](data-platform.md) van [Logboeken](data-platform-logs.md) en [metrieken](data-platform-metrics.md) zoals beschreven in [Azure monitor data platform](data-platform.md). Het bewaken van gegevens van Azure-resources kan worden geschreven naar andere locaties, ofwel voordat ze worden gekopieerd naar Azure Monitor of om extra scenario's te ondersteunen. 

## <a name="monitoring-data-locations"></a>Gegevens locaties bewaken

De volgende tabel bevat de verschillende locaties waar de bewakings gegevens in Azure worden verzonden en de verschillende methoden om deze te openen.

| Locatie | Beschrijving | Toegangs methoden |
|:---|:---|:---|:--|
| Azure Monitor metrische gegevens | Time-Series-Data Base die is geoptimaliseerd voor het analyseren van gegevens met tijds tempel. | [Metrics Explorer](metrics-getting-started.md)<br>[API voor Azure Monitor metrieken](/rest/api/monitor/metrics) |
| Azure Monitor-logboeken    | Log Analytics werk ruimte op basis van Azure Data Explorer die een krachtige analyse-engine en een uitgebreide query taal biedt. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics-API](https://dev.loganalytics.io/)<br>[Application Insights-API](https://dev.applicationinsights.io/reference/get-query) |
| Activiteitenlogboek | Gegevens uit het activiteiten logboek zijn het handigst wanneer ze naar Azure Monitor logboeken worden verzonden om deze te analyseren met andere gegevens, maar ook op de eigen manier worden verzameld, zodat deze rechtstreeks kan worden weer gegeven in de Azure Portal. | [Azure Portal](activity-log-view.md#azure-portal)<br>[API voor Azure Monitor gebeurtenissen](/rest/api/monitor/eventcategories) |
| Azure Storage | Sommige gegevens bronnen schrijven rechtstreeks naar Azure Storage en vereisen configuratie om gegevens naar Logboeken te verplaatsen. U kunt ook gegevens verzenden naar Azure Storage voor archivering en voor integratie met externe systemen.  | [Opslaganalyse](/rest/api/storageservices/storage-analytics)<br>[Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Opslagverkenner](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Gegevens verzenden naar Azure Event Hubs om deze naar andere locaties te streamen. | [Vastleggen in opslag](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor voor virtuele machines | Azure Monitor voor VM's werk belasting status gegevens worden opgeslagen op een aangepaste locatie die wordt gebruikt door de bewakings ervaring in de Azure Portal. | [Azure Portal](../insights/vminsights-overview.md)<br>[Bewakings REST API workload](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure resource Health-REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Waarschuwingen | Waarschuwingen die zijn gemaakt door Azure Monitor. | [Azure Portal](alerts-managing-alert-instances.md)<br>[REST API voor waarschuwings beheer](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Volgende stappen

- Bekijk de verschillende bronnen van [bewakings gegevens die door Azure monitor zijn verzameld](data-sources.md).
- Meer informatie over de [typen bewakings gegevens die worden verzameld door Azure monitor](data-platform.md) en hoe u deze gegevens kunt weer geven en analyseren.
