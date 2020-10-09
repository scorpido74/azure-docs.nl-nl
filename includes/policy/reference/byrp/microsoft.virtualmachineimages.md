---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7d2e6686dfb4af75440b324a2e395662cc3f658e
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91859730"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[VM Image Builder-sjablonen moeten een private link gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Controleer VM Image Builder-sjablonen waarvoor geen virtueel netwerk is geconfigureerd. Wanneer er geen virtueel netwerk is geconfigureerd, wordt er een openbaar IP gemaakt dat in plaats daarvan wordt gebruikt. Dat betekent dat resources mogelijk rechtstreeks via internet worden blootgesteld wat de kwetsbaarheid voor aanvallen kan verhogen. |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
