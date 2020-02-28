---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/26/2020
ms.author: dacoulte
ms.openlocfilehash: a9a65b967f7c6d16497c9f6f0fb53ec290a824a2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77780603"
---
|Name |Beschrijving |Effect (s) |Version |Bron |
|---|---|---|---|---|
|[Container registers moeten worden versleuteld met een door de klant beheerde sleutel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Container registers controleren waarvoor geen versleuteling is ingeschakeld met door de klant beheerde sleutels (CMK). Ga voor meer informatie over CMK-versleuteling naar: https://aka.ms/acr/CMK. |Controle, uitgeschakeld |1.0.0-Preview |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Container registers mogen geen onbeperkte netwerk toegang toestaan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Container registers controleren waarvoor geen netwerk-of VNET-regels zijn geconfigureerd en die standaard alle netwerk toegang toestaan. Container registers met ten minste één IP/firewall regel of geconfigureerd virtueel netwerk worden als compatibel beschouwd. Ga voor meer informatie over Container Registry-netwerk regels naar: https://aka.ms/acr/vnet. |Controle, uitgeschakeld |1.0.0-Preview |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
