---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198340"
---
| Resource | [Verbruiks abonnement](../articles/azure-functions/functions-scale.md#consumption-plan) | [Premium-abonnement](../articles/azure-functions/functions-scale.md#premium-plan) | [App service abonnement](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Uitschalen | Gestuurde gebeurtenis | Gestuurde gebeurtenis | [Hand matig/automatisch schalen](../articles/app-service/manage-scale-up.md) | 
| Maximum aantal exemplaren | 200 | 100 | 10-20 |
|Standaard [time-outduur](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2</sup> |
|Maximale [time-outduur](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | niet-gebonden<sup>8</sup> | niet-gebonden<sup>3</sup> |
| Maximum aantal uitgaande verbindingen (per instantie) | 600 actief (1200 totaal) | unbounded | unbounded |
| Maximale aanvraag grootte (MB)<sup>4</sup> | 100 | 100 | 100 |
| Maximale lengte van de query teken reeks<sup>4</sup> | 4096 | 4096 | 4096 |
| Maximale lengte van de aanvraag-URL<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) per instantie | 100 | 210-840 | 100-840 |
| Maxi maal geheugen (GB per instantie) | 1.5 | 3,5-14 | 1,75-14 |
| Functie-apps per plan |100 |100 |niet-gebonden<sup>5</sup> |
| [App Service-abonnementen](../articles/app-service/overview-hosting-plans.md) | 100 per [regio](https://azure.microsoft.com/global-infrastructure/regions/) |100 per resource groep |100 per resource groep |
| Opslag<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| </a> voor aangepaste domeinen per app |500<sup>7</sup> |500 |500 |
| SSL- [ondersteuning](../articles/app-service/configure-ssl-bindings.md) voor aangepaste domeinen |niet-gebonden SNI SSL verbinding opgenomen | niet-gebonden SNI SSL en 1 IP SSL verbindingen inbegrepen |niet-gebonden SNI SSL en 1 IP SSL verbindingen inbegrepen | 

<sup>1</sup> zie de [app service plan limieten](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)voor specifieke limieten voor de verschillende opties voor het app service plan.  
<sup>2</sup> standaard is de time-out voor de functies 1. x runtime in een app service plan ongebonden.  
<sup>3</sup> vereist dat het app service-abonnement is ingesteld op [Always on](../articles/azure-functions/functions-scale.md#always-on). Betaal op basis van standaard [tarieven](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> deze limieten worden [ingesteld op de host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> het werkelijke aantal functie-apps dat u kunt hosten, is afhankelijk van de activiteit van de apps, de grootte van de computer instanties en het bijbehorende resource gebruik.  
<sup>6</sup> de opslag limiet is de totale inhouds grootte in tijdelijke opslag voor alle apps in hetzelfde app service-abonnement. Het verbruiks abonnement gebruikt Azure Files voor tijdelijke opslag.  
<sup>7</sup> wanneer uw functie-app wordt gehost in een [verbruiks abonnement](../articles/azure-functions/functions-scale.md#consumption-plan), wordt alleen de CNAME-optie ondersteund. Voor functie-apps in een [Premium-abonnement](../articles/azure-functions/functions-scale.md#premium-plan) of een app service- [abonnement](../articles/azure-functions/functions-scale.md#app-service-plan)kunt u een aangepast domein toewijzen met BEhulp van een CNAME-of a-record.  
<sup>8</sup> gegarandeerd maxi maal 60 minuten.