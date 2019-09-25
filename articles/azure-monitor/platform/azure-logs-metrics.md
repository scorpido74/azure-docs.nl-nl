---
title: Logboeken en metrische gegevens in azure | Microsoft Docs
description: Overzicht van Diagnostische logboeken in azure waarmee uitgebreide, frequente gegevens over de werking van een Azure-resource worden verstrekt.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 3fe220506e074f881a16c1805d25fb4b39927488
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262360"
---
# <a name="logs-and-metrics-in-azure"></a>Logboeken en metrische gegevens in azure
Er zijn verschillende [Logboeken](data-platform-logs.md) en [metrische gegevens](data-platform-metrics.md)

Het bewaken van toepassingen en services in azure kan worden gescheiden in die zijn opgeslagen in het [Azure-gegevens platform](data-platform.md). 

Logboeken en metrische gegevens kunnen worden onderverdeeld in twee categorieën

- Platform-logboeken en metrische gegevens die automatisch worden gegenereerd zonder dat er configuratie hoeft te worden uitgevoerd, anders dan alleen 



| Laag | Platform logboeken | Metrische platform gegevens | Aangepaste logboeken | Aangepaste metrische gegevens |
|:---|:---|:---|:---|:---|
| Toepassing  | | | | |
| Gastbesturingssysteem     | Heartbeat |  | Uitbrei ding voor diagnostische gegevens<br>Log Analytics-agent | Uitbrei ding voor diagnostische gegevens |
| Resource     | [Resource logboeken](resource-logs-overview.md)<br>(specifiek voor elke service) | [Metrische gegevens van resources](metrics-supported.md)<br>(specifiek voor elke service) | | [Aangepaste metrische gegevens](metrics-custom-overview.md) |
| Subscription | [Activiteitenlogboek](activity-logs-overview.md) | | | |
| Tenant       | 

## <a name="next-steps"></a>Volgende stappen

* [Diagnostische logboeken van de resource naar Stream **Event Hubs**](resource-logs-stream-event-hubs.md)
* [Diagnostische instellingen voor resources met behulp van de REST-API van Azure Monitor wijzigt](https://docs.microsoft.com/rest/api/monitor/)
* [Logboeken analyseren vanuit Azure Storage met Azure Monitor](collect-azure-metrics-logs.md)
