---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 37a61e270f95ffdc420536046e0ce1eda1e489b4
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391473"
---
| Resource |[Verbruiks abonnement](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premium-abonnement](../articles/azure-functions/functions-scale.md#premium-plan)|[Toegewezen plan](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|Standaard [time-outduur](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|Maximale [time-outduur](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | niet-gebonden<sup>7</sup> | niet-gebonden<sup>2</sup> | unbounded | unbounded |
| Maximum aantal uitgaande verbindingen (per instantie) | 600 actief (1200 totaal) | unbounded | unbounded | unbounded | unbounded |
| Maximale aanvraag grootte (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | Is afhankelijk van het cluster |
| Maximale lengte van de query teken reeks<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | Is afhankelijk van het cluster |
| Maximale lengte van de aanvraag-URL<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Is afhankelijk van het cluster |
|[ACU](../articles/virtual-machines/windows/acu.md) per instantie | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [Prijzen voor AKS](https://azure.microsoft.com/pricing/details/container-service/) |
| Maxi maal geheugen (GB per instantie) | 1.5 | 3,5-14 | 1,75-14 | 3,5-14 | Elk knoop punt wordt ondersteund |
| Functie-apps per plan |100 |100 |niet-gebonden<sup>4</sup> | unbounded | unbounded |
| [App Service-abonnementen](../articles/app-service/overview-hosting-plans.md) | 100 per [regio](https://azure.microsoft.com/global-infrastructure/regions/) |100 per resource groep |100 per resource groep | - | - |
| Opslag<sup>5</sup> |5 TB |250 GB |50-1000 GB | 1 TB | N.v.t. |
| Aangepaste domeinen per app</a> |500<sup>6</sup> |500 |500 | 500 | N.v.t. |
| SSL- [ondersteuning](../articles/app-service/configure-ssl-bindings.md) voor aangepaste domeinen |niet-gebonden SNI SSL verbinding opgenomen | niet-gebonden SNI SSL en 1 IP SSL verbindingen inbegrepen |niet-gebonden SNI SSL en 1 IP SSL verbindingen inbegrepen | niet-gebonden SNI SSL en 1 IP SSL verbindingen inbegrepen | N.v.t. |

<sup>1</sup> standaard is de time-out voor de functies 1. x runtime in een app service plan ongebonden.  
<sup>2</sup> vereist dat het app service-abonnement is ingesteld op [Always on](../articles/azure-functions/functions-scale.md#always-on). Betaal op basis van standaard [tarieven](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>3</sup> deze limieten worden [ingesteld op de host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> het werkelijke aantal functie-apps dat u kunt hosten, is afhankelijk van de activiteit van de apps, de grootte van de computer instanties en het bijbehorende resource gebruik.  
<sup>5</sup> de opslag limiet is de totale inhouds grootte in tijdelijke opslag voor alle apps in hetzelfde app service-abonnement. Het verbruiks abonnement gebruikt Azure Files voor tijdelijke opslag.  
<sup>6</sup> wanneer uw functie-app wordt gehost in een [verbruiks abonnement](../articles/azure-functions/functions-scale.md#consumption-plan), wordt alleen de CNAME-optie ondersteund. Voor functie-apps in een [Premium-abonnement](../articles/azure-functions/functions-scale.md#premium-plan) of een app service- [abonnement](../articles/azure-functions/functions-scale.md#app-service-plan)kunt u een aangepast domein toewijzen met BEhulp van een CNAME-of a-record.  
<sup>7</sup> gegarandeerd een tijd van maxi maal 60 minuten.  
<sup>8</sup> werk nemers zijn rollen die klant-apps hosten. Werk nemers zijn beschikbaar in drie vaste formaten: één vCPU/3,5 GB RAM; Twee vCPU/7 GB RAM; Vier vCPU/14 GB RAM.
