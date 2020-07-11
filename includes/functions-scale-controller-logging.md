---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169902"
---
| | |
|--|--|
|**`<DESTINATION>`**| De bestemming waarnaar de logboeken worden verzonden. Geldige waarden zijn `AppInsights` en `Blob` .<br/>Wanneer u gebruikt `AppInsights` , moet u ervoor zorgen dat [Application Insights is ingeschakeld in uw functie-app](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration).<br/>Wanneer u de bestemming instelt op `Blob` , worden logboeken gemaakt in een BLOB-container met `azure-functions-scale-controller` de naam in het standaard opslag account dat is ingesteld in de `AzureWebJobsStorage` toepassings instelling. |
|**`<VERBOSITY>`** | Hiermee geeft u het niveau van logboek registratie op. Ondersteunde waarden zijn `None` , `Warning` en `Verbose` .<br/>Als deze instelling `Verbose` is ingesteld op, registreert de schaal controller een reden voor elke wijziging in het aantal werk nemers, evenals informatie over de triggers die in deze beslissingen zijn opgenomen. Uitgebreide logboeken bevatten trigger waarschuwingen en de hashes die worden gebruikt door de triggers vóór en nadat de schaal controller wordt uitgevoerd. |

> [!CAUTION]
> Laat logboek registratie van schaal controller niet ingeschakeld. Schakel logboek registratie in totdat u voldoende gegevens hebt verzameld om inzicht te krijgen in de werking van de schaal controller en deze vervolgens uit te scha kelen.