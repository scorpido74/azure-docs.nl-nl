---
title: bestand opnemen
description: bestand opnemen
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198323"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Time-outduur van functie-apps 

De time-outduur van een functie-app wordt gedefinieerd door de `functionTimeout` eigenschap in het projectbestand [host.json.](../articles/azure-functions/functions-host-json.md#functiontimeout) In de volgende tabel worden de standaard- en maximumwaarden in minuten weergegeven voor beide abonnementen en de verschillende runtime-versies:

| Plannen | Runtime-versie | Standaard | Maximum |
|------|---------|---------|---------|
| Verbruik | 1.x | 5 | 10 |
| Verbruik | 2.x | 5 | 10 |
| Verbruik | 3.x | 5 | 10 |
| Premium | 1.x | 30 | Onbeperkt |
| Premium | 2.x | 30 | Onbeperkt |
| Premium | 3.x | 30 | Onbeperkt |
| App Service | 1.x | Onbeperkt | Onbeperkt |
| App Service | 2.x | 30 | Onbeperkt |
| App Service | 3.x | 30 | Onbeperkt |

> [!NOTE] 
> Ongeacht de time-outinstelling van de functie-app is 230 seconden de maximale tijd die een HTTP-geactiveerde functie kan nemen om op een verzoek te reageren. Dit komt door de [standaard inactieve time-out van Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Voor langere verwerkingstijden u overwegen het [asyncpatroon duurzame functies te](../articles/azure-functions/durable/durable-functions-overview.md#async-http) gebruiken of [het werkelijke werk uit te stellen en een onmiddellijke reactie terug te sturen.](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)
