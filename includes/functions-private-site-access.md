---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 2d66e7f497f85141de172c59b67676e1bb93955e
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578811"
---
[Azure Private Endpoint](../articles/private-link/private-endpoint-overview.md) is een netwerkinterface die u privé en veilig verbindt met een service die wordt ondersteund door Azure Private Link.  Private Endpoint maakt gebruik van een privé-IP-adres van uw virtuele netwerk, waarbij de service effectief in uw virtuele netwerk wordt geplaatst.

U kunt een privé-eind punt gebruiken voor uw functies die worden gehost in de [Premium](../articles/azure-functions/functions-premium-plan.md) -en [app service](../articles/azure-functions/functions-scale.md#app-service-plan) -abonnementen.

Bij het maken van een verbinding voor een inkomend particulier eind punt voor functions, hebt u ook een DNS-record nodig om het privé adres op te lossen.  Er wordt standaard een privé-DNS-record voor u gemaakt bij het maken van een persoonlijk eind punt met behulp van de Azure Portal.

Zie voor meer informatie over het [gebruik van privé-eind punten voor web apps](../articles/app-service/networking/private-endpoint.md).