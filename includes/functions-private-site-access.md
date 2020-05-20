---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648812"
---
Toegang via een persoonlijke site verwijst naar het toegankelijk maken van uw app vanaf een particulier netwerk, zoals een virtueel Azure-netwerk.

* Toegang tot persoonlijke sites is beschikbaar in de [Premium](../articles/azure-functions/functions-premium-plan.md)-, [verbruiks](../articles/azure-functions/functions-scale.md#consumption-plan)-en [app service](../articles/azure-functions/functions-scale.md#app-service-plan) plannen wanneer service-eind punten zijn geconfigureerd.
    * Service-eind punten kunnen per app worden geconfigureerd onder **platform functies**  >  **netwerk**  >  **toegangs beperkingen configureren**  >  **regel toevoegen**. Virtuele netwerken kunnen nu worden geselecteerd als regel type.
    * Zie [service-eind punten voor virtuele netwerken](../articles/virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie.
    * Houd er bij service-eind punten voor dat uw functie nog steeds volledige uitgaande toegang tot het internet heeft, zelfs als de virtuele netwerk integratie is geconfigureerd.
* Toegang tot privé-sites is ook beschikbaar in een App Service Environment dat is geconfigureerd met een interne load balancer (ILB). Zie [een interne Load Balancer met een app service Environment maken en gebruiken](../articles/app-service/environment/create-ilb-ase.md)voor meer informatie.

Zie voor meer informatie over het instellen van toegang tot persoonlijke sites, [Azure functions toegang tot de persoonlijke site tot stand brengen](../articles/azure-functions/functions-create-private-site-access.md).