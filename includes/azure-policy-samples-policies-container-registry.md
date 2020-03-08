---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2020
ms.author: dacoulte
ms.openlocfilehash: 45cb4e589a00a2ab17fee280bb6066eaee4f24c9
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669083"
---
|Naam |Beschrijving |Effect (s) |Version |GitHub |
|---|---|---|---|---|
|[Container registers moeten worden versleuteld met een door de klant beheerde sleutel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Container registers controleren waarvoor geen versleuteling is ingeschakeld met door de klant beheerde sleutels (CMK). Ga voor meer informatie over CMK-versleuteling naar: https://aka.ms/acr/CMK. |Controle, uitgeschakeld |1.0.0-Preview |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Container registers mogen geen onbeperkte netwerk toegang toestaan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Container registers controleren waarvoor geen netwerk-of VNET-regels zijn geconfigureerd en die standaard alle netwerk toegang toestaan. Container registers met ten minste één IP/firewall regel of geconfigureerd virtueel netwerk worden als compatibel beschouwd. Ga voor meer informatie over Container Registry-netwerk regels naar: https://aka.ms/acr/vnet. |Controle, uitgeschakeld |1.0.0-Preview |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
