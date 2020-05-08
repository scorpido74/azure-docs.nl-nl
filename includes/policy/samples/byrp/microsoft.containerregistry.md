---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d6571a79b8cf0bfa3386271cc5646cad386d18e4
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82965999"
---
|Naam |Beschrijving |Effect (s) |Versie |GitHub |
|---|---|---|---|---|
|[Container registers moeten worden versleuteld met een door de klant beheerde sleutel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Container registers controleren waarvoor geen versleuteling is ingeschakeld met door de klant beheerde sleutels (CMK). Ga voor meer informatie over CMK-versleuteling naar: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Controle, uitgeschakeld |1.0.0-Preview |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Container registers mogen geen onbeperkte netwerk toegang toestaan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Container registers controleren waarvoor geen netwerk-of VNET-regels zijn geconfigureerd en die standaard alle netwerk toegang toestaan. Container registers met ten minste één IP/firewall regel of geconfigureerd virtueel netwerk worden als compatibel beschouwd. Ga voor meer informatie over Container Registry-netwerk regels naar: [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Controle, uitgeschakeld |1.0.0-Preview |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Container registers moeten persoonlijke koppelingen gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Container registers controleren die niet ten minste één goedgekeurde persoonlijke eindpunt verbinding hebben. Clients in een virtueel netwerk hebben veilig toegang tot bronnen met privé-eindpunt verbindingen via persoonlijke koppelingen. Ga voor meer informatie naar: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Controle, uitgeschakeld |1.0.0-Preview |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[Container Registry moet een service-eind punt van een virtueel netwerk gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Dit beleid controleert alle Container Registry die niet zijn geconfigureerd voor het gebruik van een service-eind punt voor een virtueel netwerk. |Controle, uitgeschakeld |1.0.0-Preview |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
