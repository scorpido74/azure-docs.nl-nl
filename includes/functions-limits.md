---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198340"
---
| Resource | [Consumptieplan](../articles/azure-functions/functions-scale.md#consumption-plan) | [Premium-abonnement](../articles/azure-functions/functions-scale.md#premium-plan) | [App-serviceabonnement](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Uitschalen | Gebeurtenisgestuurd | Gebeurtenisgestuurd | [Handmatig/automatisch schalen](../articles/app-service/manage-scale-up.md) | 
| Max. | 200 | 100 | 10-20 |
|[Standaardtime-outduur](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2.</sup> |
|Maximale [time-outduur](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | onbegrensde<sup>8</sup> | onbegrensde<sup>3</sup> |
| Maximale uitgaande verbindingen (per instantie) | 600 actief (1200 in totaal) | onbegrensd | onbegrensd |
| Maximale aanvraaggrootte (MB)<sup>4</sup> | 100 | 100 | 100 |
| Maximale querytekenreekslengte<sup>4</sup> | 4096 | 4096 | 4096 |
| Url-lengte<sup>4</sup> max. | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) per instantie | 100 | 210-840 | 100-840 |
| Maximaal geheugen (GB per instantie) | 1.5 | 3.5-14 | 1.75-14 |
| Functie-apps per abonnement |100 |100 |onbegrensde<sup>5</sup> |
| [App Service-abonnementen](../articles/app-service/overview-hosting-plans.md) | 100 per [regio](https://azure.microsoft.com/global-infrastructure/regions/) |100 per resourcegroep |100 per resourcegroep |
| Opslag<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Aangepaste domeinen per app</a> |500<sup>7</sup> |500 |500 |
| [Ssl-ondersteuning](../articles/app-service/configure-ssl-bindings.md) voor aangepaste domeinen |niet-begrensde SNI SSL-verbinding inbegrepen | niet-begrensde SNI SSL- en 1 IP SSL-verbindingen inbegrepen |niet-begrensde SNI SSL- en 1 IP SSL-verbindingen inbegrepen | 

<sup>1</sup> Zie de limieten voor [app-serviceplannen](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)voor specifieke limieten voor de verschillende app-serviceplannen.  
<sup>2</sup> Standaard is de time-out voor de taken 1.x runtime in een App Service-abonnement onbegrensd.  
<sup>3</sup> Vereist dat het App Service-abonnement is ingesteld [op Altijd aan](../articles/azure-functions/functions-scale.md#always-on). Betaal tegen [standaardtarieven.](https://azure.microsoft.com/pricing/details/app-service/)  
<sup>4</sup> Deze limieten zijn [ingesteld in de host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> Het werkelijke aantal functie-apps dat u hosten, is afhankelijk van de activiteit van de apps, de grootte van de machine-exemplaren en het bijbehorende resourcegebruik.  
<sup>6</sup> De opslaglimiet is de totale inhoudsgrootte in tijdelijke opslag voor alle apps in hetzelfde App Service-abonnement. Het verbruiksplan maakt gebruik van Azure Files voor tijdelijke opslag.  
<sup>7</sup> Wanneer uw functie-app wordt gehost in een [verbruiksabonnement,](../articles/azure-functions/functions-scale.md#consumption-plan)wordt alleen de CNAME-optie ondersteund. Voor functie-apps in een [Premium-abonnement](../articles/azure-functions/functions-scale.md#premium-plan) of een [App Service-abonnement](../articles/azure-functions/functions-scale.md#app-service-plan)u een aangepast domein toewijzen met behulp van een CNAME- of een A-record.  
<sup>8</sup> Gegarandeerd voor maximaal 60 minuten.