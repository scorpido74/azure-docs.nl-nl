---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8920752a6adfe5f3259c4bf701173d2d817aa7da
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277142"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Containerregisters moeten worden versleuteld met een door de klant beheerde sleutel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Controleer containerregisters waarvoor geen versleuteling is ingeschakeld met door klanten beheerde sleutels (CMK). Voor meer informatie bezoekt u: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Containerregisters mogen geen onbeperkte netwerktoegang toestaan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Controleer containerregisters waarvoor geen netwerkregels (IP of VNET) zijn geconfigureerd en die niet standaard netwerktoegang toestaan. Containerregisters met minstens één geconfigureerde IP-/firewallregel of viruteel netwerk, worden beschouwd als conform. Ga voor meer informatie over netwerkregels van containerregisters naar: [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Containerregisters moeten gebruikmaken van persoonlijke koppelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Controleer containerregisters die niet minstens één goedgekeurde privé-eindpuntverbinding hebben. Clients in een virtueel netwerk hebben beveiligde toegang tot resources met privé-eindpuntverbindingen door middel van privékoppelingen. Voor meer informatie gaat u naar: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
