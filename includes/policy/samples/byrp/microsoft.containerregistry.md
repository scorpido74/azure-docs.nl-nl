---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 85ffe8c4477a2226f570323f6f7beefa5f513a4a
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84712004"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Containerregisters moeten worden versleuteld met een door de klant beheerde sleutel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Controleer containerregisters waarvoor geen versleuteling is ingeschakeld met door klanten beheerde sleutels (CMK). Voor meer informatie bezoekt u: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Containerregisters mogen geen onbeperkte netwerktoegang toestaan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Controleer containerregisters waarvoor geen netwerkregels (IP of VNET) zijn geconfigureerd en die niet standaard netwerktoegang toestaan. Containerregisters met minstens één geconfigureerde IP-/firewallregel of viruteel netwerk, worden beschouwd als conform. Ga voor meer informatie over netwerkregels van containerregisters naar: [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Containerregisters moeten gebruikmaken van persoonlijke koppelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Controleer containerregisters die niet minstens één goedgekeurde privé-eindpuntverbinding hebben. Clients in een virtueel netwerk hebben beveiligde toegang tot resources met privé-eindpuntverbindingen door middel van privékoppelingen. Voor meer informatie gaat u naar: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[Container Registry moet gebruikmaken van een virtuele-netwerkservice-eindpunt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Met dit beleid worden alle exemplaren van Container Registry gecontroleerd die niet zijn geconfigureerd voor het gebruik van een service-eindpunt voor een virtueel netwerk. |Controle, uitgeschakeld |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
