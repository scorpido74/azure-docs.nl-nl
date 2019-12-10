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
ms.openlocfilehash: fded43bb655cefda508b82eca94522730ab6da00
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941692"
---
## <a name="timeout"></a>Time-outperiode van functie-app 

De time-outwaarde van een functie-app wordt gedefinieerd door de eigenschap functionTimeout in het project van de [host. json](../articles/azure-functions/functions-host-json.md#functiontimeout) -bestand. In de volgende tabel ziet u de standaard-en maximum waarden in minuten voor beide plannen en in runtime versies:

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
