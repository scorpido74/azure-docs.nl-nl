---
title: Gegevenslocaties bewaken in Azure Monitor | Microsoft Documenten
description: Beschrijft de verschillende locaties waar bewakingsgegevens in Azure worden opgeslagen, waaronder het Azure Monitor-gegevensplatform.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666612"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Gegevenslocaties bewaken in Azure Monitor

Azure Monitor is gebaseerd op een [gegevensplatform](data-platform.md) van [logboeken](data-platform-logs.md) en [statistieken zoals](data-platform-metrics.md) beschreven in [Azure Monitor-gegevensplatform.](data-platform.md) Het bewaken van gegevens uit Azure-bronnen kan echter naar andere locaties worden geschreven, voordat ze naar Azure Monitor worden gekopieerd of om aanvullende scenario's te ondersteunen. 

## <a name="monitoring-data-locations"></a>Gegevenslocaties controleren

In de volgende tabel worden de verschillende locaties aangegeven waar bewakingsgegevens in Azure worden verzonden en de verschillende methoden voor toegang tot deze gegevens.

| Locatie | Beschrijving | Toegangsmethoden |
|:---|:---|:---|:--|
| Azure-monitorstatistieken | Tijdreeksdatabase die is geoptimaliseerd voor het analyseren van tijdstempelgegevens. | [Metrics Explorer](metrics-getting-started.md)<br>[API azure monitormetrics](/rest/api/monitor/metrics) |
| Azure Monitor-logboeken    | Log Analytics-werkruimte die is gebaseerd op Azure Data Explorer, die een krachtige analyse-engine en uitgebreide querytaal biedt. | [Logboekanalyse](../log-query/portals.md)<br>[Api voor logboekanalyse](https://dev.loganalytics.io/)<br>[API voor toepassingsinzichten](https://dev.applicationinsights.io/reference/get-query) |
| Activiteitenlogboek | Gegevens uit het activiteitenlogboek zijn het handigst wanneer ze naar Azure Monitor Logs worden verzonden om deze te analyseren met andere gegevens, maar het wordt ook op zichzelf verzameld, zodat deze rechtstreeks in de Azure-portal kunnen worden bekeken. | [Azure-portal](activity-log-view.md#azure-portal)<br>[API azure monitorgebeurtenissen](/rest/api/monitor/eventcategories) |
| Azure Storage | Sommige gegevensbronnen schrijven rechtstreeks naar Azure-opslag en vereisen configuratie om gegevens naar logboeken te verplaatsen. U ook gegevens verzenden naar Azure-opslag voor archivering en voor integratie met externe systemen.  | [Storage Analytics](/rest/api/storageservices/storage-analytics)<br>[Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Stuur gegevens naar Azure Event Hubs om deze naar andere locaties te streamen. | [Vastleggen op opslag](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor voor virtuele machines | Azure Monitor voor VM's slaat statusgegevens van workloads op op een aangepaste locatie die wordt gebruikt door de bewakingservaring in de Azure-portal. | [Azure-portal](../insights/vminsights-overview.md)<br>[RestAPI voor workloadmonitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[REST-API voor Azure-bronstatus](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Waarschuwingen | Waarschuwingen die zijn gemaakt door Azure Monitor. | [Azure-portal](alerts-managing-alert-instances.md)<br>[WAARSCHUWINGEN Beheer REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Volgende stappen

- Bekijk de verschillende bronnen van [bewakingsgegevens die zijn verzameld door Azure Monitor.](data-sources.md)
- Meer informatie over de [typen bewakingsgegevens die door Azure Monitor worden verzameld](data-platform.md) en hoe u deze gegevens bekijken en analyseren.
