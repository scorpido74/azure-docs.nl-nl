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
ms.openlocfilehash: 3501ff3f92ae045019df2766bbcf7fc2c3fec5b0
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768912"
---
## <a name="timeout"></a>Time-outperiode van functie-app 

De time-outwaarde van een functie-app wordt gedefinieerd door de eigenschap `functionTimeout` in het bestand [host. json](../articles/azure-functions/functions-host-json.md#functiontimeout) -project. In de volgende tabel ziet u de standaard-en maximum waarden in minuten voor beide plannen en de verschillende runtime versies:

| Plan | Runtime versie | Standaard | Maximum |
|------|---------|---------|---------|
| Verbruik | 1.x | 5 | 10 |
| Verbruik | 2.x | 5 | 10 |
| Verbruik | controleert | 5 | 10 |
| App Service | 1.x | Onbeperkt | Onbeperkt |
| App Service | 2.x | 30 | Onbeperkt |
| App Service | controleert | 30 | Onbeperkt |

> [!NOTE] 
> Ongeacht de time-outinstelling van de functie-app is 230 seconden de maximale tijds duur die een door HTTP geactiveerde functie kan ondernemen om te reageren op een aanvraag. Dit komt door de [standaard time-out voor inactiviteit van Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Overweeg het [Durable functions async-patroon](../articles/azure-functions/durable/durable-functions-overview.md#async-http) te gebruiken of [de werkelijke hoeveelheid werk uit te stellen en een onmiddellijke reactie te retour neren](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)voor langere verwerkings tijden.
