---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 74747d51cdf584d909b2d9a70dfd414bda29feea
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86273932"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Met dit beleid worden exemplaren van Azure Database for MariaDB gecontroleerd waarvoor geografisch redundante back-up niet is ingeschakeld. |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[De MariaDB-server moet gebruikmaken van een service-eindpunt voor een virtueel netwerk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Met dit beleid worden alle exemplaren van MariaDB-servers gecontroleerd die niet zijn geconfigureerd voor het gebruik van een service-eindpunt voor een virtueel netwerk. Ga naar [https://aka.ms/mariadbvirtualnetwork](https://aka.ms/mariadbvirtualnetwork) voor meer informatie. |AuditIfNotExists, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Het privé-eindpunt moet worden ingeschakeld voor MariaDB-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Met dit beleid worden alle exemplaren van MariaDB-servers gecontroleerd die niet zijn geconfigureerd voor het gebruik van een privé-eindpunt. Ga naar [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink) voor meer informatie. |AuditIfNotExists, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Openbare netwerktoegang moet worden uitgeschakeld voor MariaDB-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Met dit beleid worden MariaDB-servers in uw omgeving gecontroleerd waarvoor openbare netwerktoegang is ingeschakeld. Ga naar [https://go.microsoft.com/fwlink/?linkid=2119542](https://go.microsoft.com/fwlink/?linkid=2119542) voor meer informatie. |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
